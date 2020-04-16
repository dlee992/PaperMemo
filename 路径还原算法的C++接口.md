# 路径还原算法的C++接口 1.1(20200416)

## 前置条件

1. 准备3个描述路径图的csv文件(每个文件的第一行作为描述信息，不存储实际数据)
   1. edge.csv：每行一条基础边，格式为 hex1,name1,type1,hex2,name2,type2
   2. mutual.csv：每行一条对向边，格式为 hex1,name1,type1,hex2,name2,type2
   3. mileage.csv：每行一个里程信息，格式为 hex,name,mileage

## 接口描述

接口文件PathRestoration.h大致如下：

```C++
class PathRestoration {
public:
  
  PathRestoration(
    std::string & enStationId,
    std::string & enTime,
    std::string & exStationId,
    std::string & exTime,
    std::string & edgePath,
    std::string & mutualPath,
    std::string & mileagePath,
    double modifyCost,
    double addCost,
    double deleteCost,
    double deleteCost2,
    double deleteEndCost,
    const std::vector<std::pair<std::string, std::string> > & gantryInputs
  );

  int pathRestorationMethod(std::vector<std::pair<std::string, std::string> > & gantryOutputs);
};

```



### 参数说明

| 参数名        | 含义                                                         | 正常示例                                                     | 允许的异常示例                                               |
| ------------- | :----------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| enStationId   | 入口收费站                                                   | G0035370030040                                               | 空字符串                                                     |
| enTime        | 经过入口收费站的时间戳                                       | 2020-01-22 16:35:31                                          | 空字符串                                                     |
| exStationId   | 出口收费站                                                   | G0035370030010                                               | 空字符串                                                     |
| exTime        | 经过出口收费站的时间戳                                       | 2020-01-22 17:20:09                                          | 空字符串                                                     |
| edgePath      | 描述基础边的文件路径                                         | /path/to/edge.csv                                            | 不允许任何异常                                               |
| mutualPath    | 描述对向边的文件路径                                         | /path/to/mutual.csv                                          | 不允许任何异常                                               |
| mileagePath   | 描述里程的文件路径                                           | /path/to/mileage.csv                                         | 不允许任何异常                                               |
| modifyCost    | 修改为对向的代价值                                           | 0.01                                                         | 大小可调节                                                   |
| addCost       | 新增节点的代价值                                             | 0.1                                                          | 大小可调节                                                   |
| DeleteCost    | 删除节点的代价值                                             | 500                                                          | 大小可调节                                                   |
| DeleteCost2   | 删除节点的代价值(?)                                          | 2                                                            | 大小可调节                                                   |
| DeleteEndCost | 删除路径两端的代价值                                         | 100000                                                       | 大小可调节                                                   |
| gantryInputs  | 输入的门架信息(不含入口、出口收费站)                         | [<“3D200B”,“2020-01-22 16:45:00”>,<“3C2003”,”2020-01-22 17:00:00">] | hex不可为空，时间戳可以为空                                  |
| gantryOutputs | 还原后的门架信息(含入口、出口收费站)，输出为一个列表，每个成员是<hex,source> | [<“G0035370030040”,”0">,<“3D200B”,“0”>,<“3D200C”,”2”>,<“2D200D”,”1”>,<“G0035370030010”,”0”>] | 如果还原失败，返回值无意义。补充source含义：0代表已有节点，1代表新增节点，2代表改为对向的节点 |
| 返回值        | 表明是否还原成功，以及失败的代号                             | 0                                                            | -1：缺少入口收费站；-2：存在未知门架；-3：缺少出口收费站，-4：其他原因导致的算法还原失败 |



## 后置条件

1. 暂无。

