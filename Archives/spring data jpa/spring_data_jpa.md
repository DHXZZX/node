# Spring Data Jpa

| Logical keyword       | Keyword expressions                            |
| --------------------- | ---------------------------------------------- |
| `AND`                 | `And`                                          |
| `OR`                  | `Or`                                           |
| `AFTER`               | `After`, `IsAfter`                             |
| `BEFORE`              | `Before`, `IsBefore`                           |
| `CONTAINING`          | `Containing`, `IsContaining`, `Contains`       |
| `BETWEEN`             | `Between`, `IsBetween`                         |
| `ENDING_WITH`         | `EndingWith`, `IsEndingWith`, `EndsWith`       |
| `EXISTS`              | `Exists`                                       |
| `FALSE`               | `False`, `IsFalse`                             |
| `GREATER_THAN`        | `GreaterThan`, `IsGreaterThan`                 |
| `GREATER_THAN_EQUALS` | `GreaterThanEqual`, `IsGreaterThanEqual`       |
| `IN`                  | `In`, `IsIn`                                   |
| `IS`                  | `Is`, `Equals`, (or no keyword)                |
| `IS_EMPTY`            | `IsEmpty`, `Empty`                             |
| `IS_NOT_EMPTY`        | `IsNotEmpty`, `NotEmpty`                       |
| `IS_NOT_NULL`         | `NotNull`, `IsNotNull`                         |
| `IS_NULL`             | `Null`, `IsNull`                               |
| `LESS_THAN`           | `LessThan`, `IsLessThan`                       |
| `LESS_THAN_EQUAL`     | `LessThanEqual`, `IsLessThanEqual`             |
| `LIKE`                | `Like`, `IsLike`                               |
| `NEAR`                | `Near`, `IsNear`                               |
| `NOT`                 | `Not`, `IsNot`                                 |
| `NOT_IN`              | `NotIn`, `IsNotIn`                             |
| `NOT_LIKE`            | `NotLike`, `IsNotLike`                         |
| `REGEX`               | `Regex`, `MatchesRegex`, `Matches`             |
| `STARTING_WITH`       | `StartingWith`, `IsStartingWith`, `StartsWith` |
| `TRUE`                | `True`, `IsTrue`                               |
| `WITHIN`              | `Within`, `IsWithin`                           |

`findByUserDepUuid()` 方法,框架解析流程

* 先判断userDepUuid(根据POJO规范,首字母变为小写)是否是查询实体的一个属性,如果市,根据该属性查询,不是,下一步
* 从右往左截取第一个大写字母开头的字符串(Uuid),然后检查剩下的字符串是否为查询的一个属性,如果是,根据该属性查询,如果不是,重复第二步;最后假设user为查询实体的一个属性
* 接着处理剩下部分(DepUuid),先判断user所对应的类型是否有depUuid属性,如果有,则表示最终方法是根据`Doc.user.depUuid`的值进行查询,否则按照上一步的规则从走往左截取,最终表示根据`Doc.user.dep.uuid`的值进行查询
* 可能会存在一种特殊情况,比如Doc包含一个user属性,也有一个userDep属性,此时会存在混淆.可以明确在属性之间加上"_"以显示表达意图,比如: `findByUser_DepUuid()`   或者 `findByUserDep_uuid()` 
* 特殊参数:还可以直接在方法的参数上加入分页或者排序的参数比如:
  * `Page<UserModel>findByName(String name,Pageable pageable);`
  * `List<UserModel>findByName(String name,Sort sort);` 
* 索引参数,索引值1开始,查询中"?X"个数需要与方法定义的参数个数一致,顺序也要一致
* 命名参数:可以定义好参数名,赋值时采用@Param("参数名"),而不用管顺序
* 如果是@Query中有LIKE关键字,后面的参数需要前面或者后面加%,这样在传递参数值的时候就可以不加%
* 还可以使用@Query来指定本地查询,只要设置参数值nativeQuery=true就可以