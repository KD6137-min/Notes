# Pandas时间处理

---

## **1. 核心类型**

|类型|描述|
| ------| -------------------------------------------|
|**Timestamp**|表示单一时间点(类似于 Python 的 `datetime`​ 对象)|
|**DatetimeIndex**|​`Timestamp`​ 的集合, 表示一组时间点 |
|**Timedelta**|表示两个时间点之间的时间间隔 |
|**TimedeltaIndex**|​`Timedelta`​ 的集合 |
|**Period**|表示一个时间段(如某个月或某个季度) |
|**PeriodIndex**|​`Period`​ 的集合, 表示一组时间段 |
|**DateOffset**|表示偏移时间的工具, 用于时间的移动和对齐 |

## **2. 创建时间对象**

### (1) **Timestamp**

单一时间点: 

```python
import pandas as pd

# 创建单一时间点
ts = pd.Timestamp("2025-01-15")
print(ts)  # 输出: 2025-01-15 00:00:00
```

### (2) **DatetimeIndex**

创建一组时间点: 

```python
# 自动生成日期范围
date_range = pd.date_range("2025-01-01", periods=5, freq="D")
print(date_range)
# 输出:
# DatetimeIndex(['2025-01-01', '2025-01-02', '2025-01-03', '2025-01-04', '2025-01-05'],
# dtype='datetime64[ns]', freq='D')
```

### (3) **Timedelta**

表示时间间隔: 

```python
# 创建时间间隔
delta = pd.Timedelta(days=5, hours=3)
print(delta)  # 输出: 5 days 03:00:00
```

### (4) **Period**

表示时间段: 

```python
# 创建一个表示月份的时间段
period = pd.Period("2025-01", freq="M")
print(period)  # 输出: 2025-01
```

## **3. 时间索引DatetimeIndex**

时间对象索引, 可以方便地进行时间切片和操作

### (1) **时间索引的创建**

```python
# 创建一个带有时间索引的 Series
ts = pd.Series([1, 2, 3], index=pd.date_range("2025-01-01", periods=3, freq="D"))
print(ts)
# 输出:
# 2025-01-01    1
# 2025-01-02    2
# 2025-01-03    3
# Freq: D, dtype: int64
```

### (2) **时间切片**

可以直接按时间段提取数据: 

```python
# 提取某一天的数据
print(ts["2025-01-02"])  # 输出: 2

# 提取一段时间的数据
print(ts["2025-01-01":"2025-01-02"])
# 输出:
# 2025-01-01    1
# 2025-01-02    2
# Freq: D, dtype: int64
```

---

## **4. 频率与偏移**

### (1) **频率 (Frequency)**

|Pandas 提供了多种时间频率, 常见的包括: ||
|频率代码|描述|
| -----------------------------------------| --------|
|​`D`​|天|
|​`B`​|工作日|
|​`H`​|小时|
|​`T`​|分钟|
|​`S`​|秒|
|​`M`​|月末|
|​`MS`​|月初|
|​`Q`​|季度末|
|​`A`​|年末|

### (2) **偏移 (Offsets)**

偏移用于调整时间序列, 常见的有: 

```python
from pandas.tseries.offsets import MonthEnd, Day

# 对齐到月末
print(pd.Timestamp("2025-01-15") + MonthEnd(1))  # 输出: 2025-01-31

# 按天偏移
print(pd.Timestamp("2025-01-15") + Day(5))  # 输出: 2025-01-20
```

## **5. 时间差操作**

### (1) **计算时间差**

```python
start = pd.Timestamp("2025-01-01")
end = pd.Timestamp("2025-01-10")

delta = end - start
print(delta)  # 输出: 9 days 00:00:00
```

### (2) **对时间序列进行时间差计算**

```python
dates = pd.date_range("2025-01-01", periods=3, freq="D")
ts = pd.Series([1, 2, 3], index=dates)

# 时间差
diff = ts.diff()
print(diff)
```

## **6. 重采样与频率转换**

时间序列的频率可以通过重采样来调整: 

```python
# 降频: 按月汇总数据
ts = pd.Series([1, 2, 3, 4, 5], index=pd.date_range("2025-01-01", periods=5, freq="D"))
resampled = ts.resample("M").sum()
print(resampled)
```

## **7. 时区处理**

Pandas 支持对时间对象添加时区信息: 

```python
# 添加时区
ts = pd.Timestamp("2025-01-01 12:00", tz="UTC")
print(ts)  # 输出: 2025-01-01 12:00:00+00:00

# 转换时区
ts_new = ts.tz_convert("Asia/Shanghai")
print(ts_new)  # 输出: 2025-01-01 20:00:00+08:00
```
