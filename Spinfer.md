# Spinfer

## Algorithm

- [ ] 不能有效处理MACRO在字符串级别的变化：但太细微了，有意义吗？
- [ ] 看上去rule ordering的方式有点问题？应该没啥问题
- [ ] 虽然在CFG中提到了对condition的建模（处理分支和循环），但是实验中有吗？
- [ ] 注意理解控制依赖和分支/循环结构的区别，这不是同一件事，顺序结构的语句之间也有控制依赖。
- [ ] 对于context的数据依赖分析很远，能从函数签名（数据定义）---> 函数体中的某一处使用（详见dasd_smalloc/reduce)
- [ ] 并没有处理好多个variants的挑战（详见dasd_smalloc/full）
- [ ] overlapping change instances in per function的处理方式我还没理解
- [ ] 在实际进行代码转换生成之前，甚至可以考虑先适当调换代码的语句顺序，让语句链拥有一种“更一致”的顺序，也许可以生成更紧凑的规则
- [ ] 对 if的condition 基本做不了任何分析，只能直接抽象成一个抽象表达式 E。因此写出来带有If的补丁也比较抽象，开发者看上去也很可能一头雾水。

## Implementation

## Evaluation

- [ ] expect.cocci 也不是完美补丁
- [ ] 实验中反复提到out of scope的测试用例，但是又没有标明具体是哪些测试用例
- [ ] recall / precision / matching recall 是如何计算出来的？迷惑行为，FP/TP的计量单位是 ***function***  or example?
- [ ] applied partially (找到使用规则，转换也正确，但有另外一部分没有找到适用规则) / did not apply (找不到适用规则) / produced incorrect changes (找到适用规则，但转换不正确)
- [ ] 总感觉很多实验数据的修改 ***make no sense*** ！！！

### Data analysis: challenging part

- [x] btrfs_inline: full中的规则包含多种变体，删除参数比较简单，甚至能表达出pr_info(str_format, ...)中 str_format 的差异。reduced中合成的恰好是full中的第二条规则，但是在最终生成规则时，又进行了简化（允许简化的条件是什么？竟然还有简化这一步骤？），只保留了必要的表达式。reduced因为没有见过涉及到str_format的例子，因此在testing过程中无法成功转换pr_info(str_format, ...)这条语句。
- [x] cleanoom: 删除了一个OOM的错误输出语句，full中合成的规则2,**先删除了if (E0) 然后又添加了回来**，这是个实现bug？这组合成的难点在于虽然都是删除一个输出语句，其中某个参数都是str_format，并且多数含有"memory"的子串，通常被包裹在一个分支语句内，其他并无用于生成规则的明显特征了。reduced中也发生了先删除了if (E0) 然后又添加了回来的问题。
- [x] copy_from_user: 使用 **memdup_user(...)** 来替换 **kmalloc(...) 和 copy_from_user(...)** 的组合。凡涉及内存读写的，都要对返回值进行错误处理，大体是这么个情况。得到 memdup_user() 的返回值之后，有的函数直接返回对应的结构体指针，有的则是继续进行其他计算，因而产生了**变体**。对分支语句的大括号的处理？感觉rules对大括号的处理有些瑕疵，对大括号的删减并不是有正确代码层次的删减。在if体里还会掺杂一些外部的memory free语句，因而产生了**变体**。这时候不同规则的执行顺序也变得更加重要，否则会产生 False Positives。最后提到的 applied partially 情况是当某个变量的使用被删除时，对应的变量定义语句没有删除。当某个更改存在两种变体时，如果规则没有足够的上下文，会导致两条规则互相掣肘，必然导致某一条规则无法产生，因为占比较小，而占比大的规则会胜出，保留下来，但同时也会产生false positives，（详见1281399506_2010-08-09_90d7404558fb_util.c: strndup_user），打破掣肘，需要考虑函数的返回值类型，因为这个问题出现在return语句上。
- [x] d_instantiate:  看上去几近完美，为啥recall率没达到1,为啥函数还有1个漏网之鱼？
- [x] dasd_smalloc: （现象的确如此，但分析不合理）因为规则先后顺序的原因，把实际上应该拆分为两条规则的节点放到了一起，导致最终只输出了那个覆盖率大的规则，直接忽略了那个覆盖率小的规则。这例子把函数的定义文件都掏出来作为例子，部分修改就属于需要过滤的noise。打破掣肘，需要考虑函数定义中的arg list是否有个特定类型的参数。
- [x] devm_request_and_ioremap: rule里的大括号，不一定是必须要匹配的字符，可能只表达scope的限定。存在两条规则可以融合的情况，实际上如果 ... 可以匹配零个或多个语句甚至任何符合控制流关系的语句，那么的确有些规则可以合并，只是多了一条 dev_err(...) 语句而已。
  - [ ] 1367502970_2013-05-02_59bff7fb7a0b_davinci_nand.c: nand_davinci_probe: 像排比句，但错误判断写在了一起 if (!vaddr || !base) { ... } ，而spinfer没能处理这两个 instances overlapping 的算法原因是什么？
  - [ ] 1383205855_2013-10-31_0859ffc3b3cb_rt2880_wdt.c: rt288x_wdt_probe, 1383205898_2013-10-31_cd5d58108e41_timer.c: rt_timer_probe, 1401171951_2014-05-27_c829f956f14b_gpio-ep93xx.c: ep93xx_gpio_probe, 1386339133_2013-12-06_87917528cc92_spi-bcm63xx-hsspi.c: bcm63xx_hsspi_probe: 合成这个规则很简单，但被自己的规则排序困住了手脚，这可能是很多 "did not apply" 的原因。浅层原因：用一个总的补丁去适用所有examples，其实每个不同的examples适用的补丁顺序本身就应该不同，考虑到每个example的自身实际，或者上下文。像这个例子，代码里已经有了合适的错误处理，所以不需要再管错误处理的问题，只需要更换API就行了。
  - [ ] 1370864106_2013-06-10_eed5d29d7818_xilinx_emaclite.c: xemaclite_of_probe: 也是微小的变动，其实能生成对应规则，但是最后合成的规则里没有体现出来。
- [x] dfops:规则2非常奇怪，(*E2)->t_dfops，为什么非要写成这种形式，直接写成 E2->t_dfops，一样可以用。感觉rule 2和rule 3并不冲突啊，不知为何是两条规则，而且还带了一个 false positive（**实现错误？**）。rule 4就是指针为空。
  - [ ] 1531373179_2018-07-11_bcd2c9f33559_xfs_defer.c: xfs_defer_init: 这个not apply很尴尬，这就是被修改的API，它自身当然是不一样的修改了。
- [x] dmabuf: 典型案例，rule 1的context code就是函数定义。奇怪的是最后的5/7,又没有单独列出哪两个functions没有被正确处理。其中一个是metafunction的修改（详见 1475670104_2016-10-05_a4fce9cb782a_drm_prime.c.res.c: drm_gem_dmabuf_export）。**那另一个究竟是啥？没找到。**
- [x] drm_sched: 完美处理，简单粗暴，一个规则通吃。drm_sched_entity_init函数本身不需要处理。
- [x] early_memunmap: 完美处理，简单粗暴，一个规则通吃
- [ ] gpiod: 
  - [ ] 
- [ ] iio_device_alloc:
  - [ ] 
- [ ] irqdesc:
  - [ ] 
- [x] kees_timer1 (motivating example): 多条规则的产生原因就如同原文所述。考虑不同的语句顺序/有些 field initialization 缺失。
- [x] kmemdup2: 完美处理，简单粗暴，一个规则通吃
- [ ] kvfree: 这个diff种类非常稀碎，有的要检查指针非空，有的却不检查; 有的删去了指针非空检查，有的没有删去。。
  - [ ] 1435701513_2015-06-30_c859aa83113d_util.c: ipc_free: 奇葩的地方在于这个函数前后完全一致，没有任何变动。。
  - [ ] 1416661990_2014-11-22_f749303bda20_raid56.c: btrfs_alloc_stripe_hash_table: 这个修改还算合理呀，为啥不能正确转换呢？
- [ ] kzalloc2:
  - [ ] 
- [x] ldebugfs : 每个规则都是比较局限的，基本限定在一两个函数内自己使用，整体不具有明显的公共特征。
  - [ ] 1527604184_2018-05-29_b145f49f233d_ldlm_resource.c: ldlm_debugfs_setup: not apply的原因可能是一个函数内包含三个instances，不过这三个instances是不相交的，看上去可以处理。不过这三个instances的存在数据依赖，一点点创建父目录/子目录/子子目录。同时还删除了三个goto的label，这就有点太难了？不确定是方法问题还是实现问题。
- [ ] list_for_each_entry:
  - [ ] 
- [x] memdup: {kmalloc/kzalloc + memcpy} 被 kmemdup 取代。但产生了5个false positives
  - [ ] 实在没有理解这5个为啥是FP，我看都是TP嘛
- [ ] nvmem:
  - [ ] 
- [x] obd: 本质上是用 kzalloc(...) 替换 OBD_ALLOC_PTR(...) , 同时要处理大量的 error-handling code (shown in if branch)。
  - [ ] 1411071842_2014-09-18_496a51bd64eb_dir.c: ll_dir_filler : 语义上说，这个例子也不能算FP。
- [ ] pgr:
  - [ ] 
- [ ] platform_get_drvdata:
  - [ ] 
- [ ] proc_create:
  - [ ] 
- [x] skb_put: 三条规则，完美处理。分别考虑了语序和额外的 type cast。
- [ ] snd_soc:
  - [ ] 
- [ ] stop_engine:
  - [ ] 
- [ ] tcaction:
  - [ ] 
- [x] uartlite: 算是完美解决，就是diff前后API的变化和参数的对应修改，没有例外。
  - [ ] 
- [x] unpopular/at91_rtc_write: 情况类似于cpufreq_frequency_table_target，diff前后的函数名和diff前后使用的宏名称有关。
  - [ ] 
- [x] unpopular/bnx2x_pretend_func: 第一条规则直接删除了9个语句，零添加。。
  - [ ] 
- [x] ***unpopular/C_CAN_IFACE***: 将 read_reg 改写成read_reg32，将 write_reg 改写成 write_reg32。但FN高。
  - [ ] FN的原因：剥皮之后还是类似的修改，但是貌似加了一层函数封装，需要新增完整的函数定义，才有可能实现这个转换，同时还要把函数赋值给对应的函数指针。
- [x] unpopular/ClearPageReserved: 两条规则，完美处理。规则2多删除了一条 - init_page_count(E0); 
- [x] unpopular/cpufreq_frequency_table_cpuinfo: 两条规则，完美处理。语序不同。
- [x] unpopular/cpufreq_frequency_table_target: 这就是论文里的例子，diff后的函数名后缀竟然跟diff前被删去的宏的后缀保持一致，但一个大写，一个小写。堪称离谱？
  - [ ] 1467001774_2016-06-27_825773609c8a_acpi-cpufreq.c: acpi_cpufreq_fast_switch: 这diff属实离谱，别的函数diff只设计到API和参数修改，但这个函数硬生生修改了一段while循环并增加了一个额外的赋值语句。
- [x] ***unpopular/delayed_work_pending***: 给出的规则基本是把 delayed_work_pending 这个函数从 if-condition 里一删就完事。但FN很多。
  - [ ] 1356141424_2012-12-21_ba0c96cd9a36_input.c: rfkill_schedule_ratelimited: 删去了完整的 if 结构，把接下来的两条语句组合成完整的 if 结构，分别变成condition和body。
  - [ ] 1356141432_2012-12-21_348409a267e4_at91_udc.c: at91_vbus_timer
  - [ ] 1357907853_2013-01-11_ed1ac6e91a3f_qos.c: pm_qos_remove_request: 这两条都是把 if-condition 删掉，保留 if-body 里的语句。猜测其他和这条类似。
- [x] unpopular/sock_kzfree_s:那其实都对了。
  - [ ] sock.c: 这个文件是定义了diff中牵扯到的API，并不是目标代码。
- [x] vm_area:
  - [ ] 1532206131_2018-07-21_3928d4f5ee37_exec.c: __bprm_mm_init: 看上去可以正常修复呀，alloc和free部分都可以。。
  - [ ] 1532206131_2018-07-21_3928d4f5ee37_mmap.c: copy_vma: 这里的diff比较特别，把kmem_cache_alloc()替换成了vm_area_dup()，这个diff规则并没有被输出， 是缺少一定的context，和另一条输出的规则会发生替换冲突，匹配了相同的diff前的代码，但该替换成diff后的哪一种选择却无法确定。
  - [ ] 1532206131_2018-07-21_3928d4f5ee37_mmap.c: __split_vma: 问题同上。
  - [ ] 1532206131_2018-07-21_3928d4f5ee37_fork.c: dup_mmap: 问题同上，同时也是源头。
  - [ ] 1532206131_2018-07-21_3928d4f5ee37_nommu.c: split_vma: 问题同上。
- [x] wlcore: 强烈的感觉这个地方的实现出了bug，最终数据明明不可能这么低。

### Data analysis: 2018 part

- [ ] atomic_inc-53:
- [ ] buf_ioerror-58:
- [ ] dev_err-52:
- [ ] perf_evlist__mmap-69:
- [ ] pr_info-47:
- [ ] tcf_block_get-61:
- [ ] ttm_bo_validate-67:
- [ ] amdgpu_gmc_emit_flush_gpu_tlb-62:
- [ ] dm_logger_write-59:
- [ ] dma_pool_alloc-52:
- [ ] EXP0-6:
- [ ] kzalloc-47:
- [ ] perf_mmap__consume-58:
- [ ] perf_mmap__read_event-57:
- [ ] ttm_bo_init-60:
- [ ] bio_alloc_bioset-44:
- [ ] EXP0-2:
- [ ] kzalloc-57:
- [ ] mdelay-55:
- [ ] memcpy-56:
- [ ] amdgpu_irq_add_id-73:
- [ ] btree_del_cursor-72:
- [ ] defer_init-78:
- [ ] drm_dev_unref-52:
- [ ] EXP0-2-2:
- [ ] get_seconds-55:
- [ ] IRELE-67:
- [ ] platform_get_resources-56:
  - [ ] 
- [ ] random_ether_addr-84:
- [ ] return-85:
- [ ] drm_dev_unref-95:
- [ ] EXP0-7:
- [ ] EXP0-8:
- [ ] EXP0-10:
- [ ] free_bootmem-77:
- [ ] memblock_alloc-82:
- [ ] omap_dss_put_device-114:
- [ ] platform_get_drvdata-86:
- [ ] pr_err-96:
- [ ] sock_poll_wait-84:

