# Pandas时间序列重采样(resample)方法中closed、label的作用


## 目录

- Pandas重采样方法resample
- 降采样
- 升采样



Pandas提供了便捷的方式对时间序列进行重采样，根据时间粒度的变大或者变小分为降采样和升采样：

- 降采样：时间粒度变大。例如，原来是按天统计的数据，现在变成按周统计。降采样会涉及到数据的聚合，比如天数据变成周数据，那么就得对一周的7天数据聚合，聚合的方式可以是求和，求均值等等。
- 升采样：时间粒度变小。例如，原来是按周统计的数据，现在变成按天统计。升采样会涉及到数据的填充，根据填充的方法不同填充的数据也就不同。

```
下面涉及的例子，都需要导入numpy和pandas(如下)，并且对于降采样数据的聚合做简单的求和处理。
import numpy as np
import pandas as pd
```

### Pandas重采样方法resample
在Pandas里，通过resample来处理重采样，根据频率的不同(freq)会处理成降采样或者升采样。我们先来看看Resample的定义和关键参数注释：
```
resample(self, rule, how=None, axis=0, fill_method=None, closed=None, label=None, convention='start', kind=None, loffset=None, limit=None, base=0, on=None, level=None)
    Convenience method for frequency conversion and resampling of time
    series.  Object must have a datetime-like index (DatetimeIndex,
    PeriodIndex, or TimedeltaIndex), or pass datetime-like values
    to the on or level keyword.

Parameters
----------
closed : {'right', 'left'}
        Which side of bin interval is closed. The default is ‘left’ for all frequency offsets except for ‘M’, ‘A’, ‘Q’, ‘BM’, ‘BA’, ‘BQ’, and ‘W’ which all have a default of ‘right’.
label : {'right', 'left'}
        Which bin edge label to label bucket with. The default is ‘left’ for all frequency offsets except for ‘M’, ‘A’, ‘Q’, ‘BM’, ‘BA’, ‘BQ’, and ‘W’ which all have a default of ‘right’.
```

第一眼看closed和label这两个参数，会感觉云里雾里，即使看了例子也可能会觉得莫名奇妙。下面我们通过具体的降采样和升采样例子，来解读一下这个两个参数内含的玄机。

### 降采样
首先先来创建一个时间序列，起始日期是2018/01/01，一共12天，每天对应的数值分别是1到12：
```
rng = pd.date_range('20180101', periods=12)
ts = pd.Series(np.arange(1,13), index=rng)

print(ts)

#### Outputs ####
2018-01-01     1
2018-01-02     2
2018-01-03     3
2018-01-04     4
2018-01-05     5
2018-01-06     6
2018-01-07     7
2018-01-08     8
2018-01-09     9
2018-01-10    10
2018-01-11    11
2018-01-12    12
Freq: D, dtype: int32
```

下面使用resample方法来做降采样处理，频率是5天，上面提到的两个参数，都使用默认值：
```
ts_5d = ts.resample('5D').sum()
print(ts_5d)

#### Outputs ####
2018-01-01    15
2018-01-06    40
2018-01-11    23
Freq: 5D, dtype: int32
```

到这里，我相信不论是代码还是代码的结果都很好理解：无非就是每5天来个求和。在第一部分中，我们列出了closed参数的注释，从注释可知，closed默认的值是'left'。那如果把closed的值改为'right'，结果有是怎么样的？
```
ts_5d_rightclosed = ts.resample('5D', closed='right').sum()
print(ts_5d_rightclosed)

#### Outputs ####
2017-12-27     1
2018-01-01    20
2018-01-06    45
2018-01-11    12
Freq: 5D, dtype: int32
```

怎么会这样？为什么变成了四个区间？closed=right到底做了什么？
别着急，我们来一步一步看看，这其中发生了什么事情。原始的时间序列是从18年1月1号到1月12号，一共12天。以5天为单位降采样处理后，变成了三个5天，分别是：

- 第一个5天：1-2-3-4-5-6
- 第二个5天：6-7-8-9-10-11
- 第三个5天：12-13-14-15-16

实际上，这三个5天就是三个区间了。和数学里区间的概念一样，区间有开和闭的概念。在resample中，区间的开和闭，就是通过closed这个参数来控制。用数学符号表示的话:

#### closed = 'left' 左闭右开
上面的三个5天可以由以下的三个左闭右开的区间构成：

- 区间1：[1, 6)
- 区间2: [6, 11)
- 区间3：[11, 16) 例子中，时间只到12号为止，但是这里会往后补足5天

现在，在这三个区间上做数据聚合也就很好理解了。对于区间1进行求和，也就是12、13、14、15、16这5天的值求和即可。区间2和区间3也是同理。所以下面的代码就很好理解了：
```
ts_5d_leftclosed = ts.resample('5D', closed='right').sum()
print(ts_5d_leftclosed)

#### Outputs ####
2018-01-01    15
2018-01-06    40
2018-01-11    23
Freq: 5D, dtype: int32
```

#### closed = 'right' 左开右闭
上面的三个5天可以由以下的四个左开右闭的区间构成。注意，由于第一个5天是从1号到6号，但由于是左开区间，1号就落不到1到6号的那个区间，所以要往前补足：

- 区间1：(27, 1]
- 区间2：(1, 6]
- 区间3: (6, 11]
- 区间4：(11, 16]

现在，在这四个区间上做数据聚合也是一样的道理了：对于区间1，是对28，29，30，31，1这五天的值求和（这里只有1号是有值的），其余的区间也是同理，但需要注意是左开右闭。所以到这里，上面“莫名其妙”的代码和结果就好理解了。复制代码和结果如下：
```
ts_5d_rightclosed = ts.resample('5D', closed='right').sum()
print(ts_5d_rightclosed)

#### Outputs ####
2017-12-27     1
2018-01-01    20
2018-01-06    45
2018-01-11    12
Freq: 5D, dtype: int32
```

理解了clsoed的意义以后，再来理解label就so easy了。由注释可知，label的默认值是left。下面在closed='right'的基础上，将label设置为right：
```
ts_5d_rightclosed_rightlable = ts.resample('5D', closed='right', label='right').sum()
print(ts_5d_rightclosed_rightlable)

#### Outputs ####
2018-01-01     1
2018-01-06    20
2018-01-11    45
2018-01-16    12
Freq: 5D, dtype: int32
```

于label为left相比，二者结果的异同点如下：

- 相同点：一样是四个区间，每个区间的聚合的值是一样的
- 不同点：每个区间的索引不同

不难发现，label为left的时候，就以区间左边的那个日期作为索引；label，就以区间的右边那个日期作为索引。
综上，我们可以总结一下closed和label的用法和意义了：


- closed：划分区间的依据，left会划成左闭右开区间；right会划分成左开右闭的区间。一般来说，closed为right的时候，区间会比为left的时候多一个。区间划分完毕，聚合运算就在这个区间内执行。

- label：划分区间完毕，根据label的不同，区间的索引就不同。如果label为left，则区间左边的日期作为索引；如果label为right，则区间右边的日期作为索引。

### 升采样
创建一个时间序列，起始日期是2018/01/01，一共2天，每天对应的数值分别是1到2:
```
rng = pd.date_range('20180101', periods=2)
ts = pd.Series(np.arange(1,2), index=rng)

print(ts)

#### Outputs ####
2018-01-01    1
2018-01-02    2
Freq: D, dtype: int32
```

升采样就不涉及到closed和label的值，也就是会忽略(筒子们可以验证一下)，所以我们在使用的时候无需设置这两个值。对于升采样，前面也提到，主要是涉及到值的填充。有下面的四种填充方法（实际是三种）：

- 不填充。那么对应无值的地方，用NaN代替。对应的方法是asfreq。
- 用前值填充。用前面的值填充无值的地方。对应的方法是ffill或者pad。这里方便记忆，ffill的第一个f是代表forward，向前的意思
- 用后值填充。对应的方法是bfill，b代表back。

下面是一个例子：
```
ts_6h_asfreq = ts.resample('6H').asfreq()
print(ts_6h_asfreq)

ts_6h_pad = ts.resample('6H').pad()
print(ts_6h_pad)

ts_6h_ffill = ts.resample('6H').ffill()
print(ts_6h_ffill)

ts_6h_bfill = ts.resample('6H').bfill()
print(ts_6h_bfill)

#### Outputs ####
2018-01-01 00:00:00    1.0
2018-01-01 06:00:00    NaN
2018-01-01 12:00:00    NaN
2018-01-01 18:00:00    NaN
2018-01-02 00:00:00    2.0
Freq: 6H, dtype: float64
2018-01-01 00:00:00    1
2018-01-01 06:00:00    1
2018-01-01 12:00:00    1
2018-01-01 18:00:00    1
2018-01-02 00:00:00    2
Freq: 6H, dtype: int32
2018-01-01 00:00:00    1
2018-01-01 06:00:00    1
2018-01-01 12:00:00    1
2018-01-01 18:00:00    1
2018-01-02 00:00:00    2
Freq: 6H, dtype: int32
2018-01-01 00:00:00    1
2018-01-01 06:00:00    2
2018-01-01 12:00:00    2
2018-01-01 18:00:00    2
2018-01-02 00:00:00    2
Freq: 6H, dtype: int32
```

### 参考文档
[Pandas时间序列重采样(resample)方法中closed、label的作用](https://www.jianshu.com/p/061771f0afa9)
[resample源码](https://github.com/pandas-dev/pandas/blob/v0.24.2/pandas/core/resample.py#L863-L865)
