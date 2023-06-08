### res_pd_1

#### res_raw_pd

```python
#提取，客户号_案例日期；收付标志；对方行号；折合人民币；对方名称；交易渠道；现转标志
res_raw_pd=data[['partyid_casedate', 'receive_pay_cd', 'opp_acct_num', 'cnt_amt',
                 'opp_name','channel', 'cash_trans_flag']]
res_raw_pd['je_num']=res_raw_pd['cnt_amt'].astype(float)
khh_pd=res_raw_pd[['partyid_casedate']].drop_duplicates()
```

####   入账-对方名称-交易金额

**python**

```python
#入账
raw_in_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==1]

#调用data_process_2，返回以‘客户号_案例日期’和‘对方名称’分组，入账‘折合人民币’的和
#即，我行客户对其他客户的总入账流水数据，以我行客户与其他客户名称分组
res_pd_temp_1=MyUtils.data_process_2(raw_in_pd,'opp_name','je_num',['sum'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’中对某个对方客户入账‘折合人民币的和’的最大值
#即，我行客户对其他客户总入账流水数据中，金额最大的数据
res_pd_1_1=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_sum':[BasicFunc.get_max]}).loc(axis=1)['je_num_sum'].\
    rename(columns={'get_max':'dfkhmc_je_num_sum_get_max_y'}).\
    reset_index()
```

**SQL**

```hive
SELECT partyid_casedate, MAX(je_num_sum) AS dfkhmc_je_num_sum_get_max_y
FROM (
    SELECT partyid_casedate, opp_name, SUM(je_num) AS je_num_sum
    FROM res_raw_pd
    WHERE receive_pay_cd = 1
    GROUP BY partyid_casedate, opp_name
) grouped_sum
GROUP BY partyid_casedate;
```

####   全量-对方名称-交易金额

**python**

```python
#调用data_process_2，返回以‘客户号_案例日期’和‘对方名称’分组，全量‘折合人民币’的和
#即，我行客户对其他客户的总流水数据，以我行客户与其他客户名称分组
res_pd_temp_1=MyUtils.data_process_2(res_raw_pd,'opp_name','je_num',['sum'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’中对某个对方客户全量‘折合人民币的和’的最大值
#即，我行客户对其他客户总流水数据中，金额最大的数据
res_pd_1_2=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_sum':[BasicFunc.get_max]}).loc(axis=1)['je_num_sum'].\
    rename(columns={'get_max':'dfkhmc_je_num_sum_get_max_x'}).\
    reset_index()
```

**SQL**

```hive
SELECT partyid_casedate, MAX(je_num_sum) AS dfkhmc_je_num_sum_get_max_x
FROM (
    SELECT partyid_casedate, opp_name, SUM(je_num) AS je_num_sum
    FROM res_raw_pd
    GROUP BY partyid_casedate, opp_name
) grouped_sum
GROUP BY partyid_casedate;
```

####   出账-交易渠道-交易金额

**python**

```python
#出账
raw_in_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==2]

#调用data_process_2，返回以‘客户号_案例日期’和‘交易渠道’分组，出账‘折合人民币’的和
#即，我行客户在不同交易渠道的出账总流水数据，以我行客户与交易渠道分组
res_pd_temp_1=MyUtils.data_process_2(raw_in_pd,'channel','je_num',['sum'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’中不同交易渠道出账‘折合人民币的和’的最大值
#即，我行客户在不同交易渠道出账总流水数据中，金额最大的数据
res_pd_1_3=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_sum':[BasicFunc.get_max]}).loc(axis=1)['je_num_sum'].\
    rename(columns={'get_max':'channel_je_num_sum_get_max_y'}).\
    reset_index()
```

**SQL**

```hive
SELECT partyid_casedate, MAX(je_num_sum) AS channel_je_num_sum_get_max_yFROM (
    SELECT partyid_casedate, channel, SUM(je_num) AS je_num_sum
    FROM res_raw_pd
    WHERE receive_pay_cd = 2
    GROUP BY partyid_casedate, channel
) grouped_sum
GROUP BY partyid_casedate;
```

####   全量-交易渠道-交易金额

**python**

```python
#调用data_process_2，返回以‘客户号_案例日期’和‘交易渠道’分组，全量‘折合人民币’的和
#即，我行客户在不同交易渠道的总流水数据，以我行客户与交易渠道分组
res_pd_temp_1=MyUtils.data_process_2(res_raw_pd,'channel','je_num',['sum'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’中不同交易渠道全量‘折合人民币的和’的最大值
#即，我行客户在不同交易渠道总流水数据中，金额最大的数据
res_pd_1_4=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_sum':[BasicFunc.get_max]}).loc(axis=1)['je_num_sum'].\
    rename(columns={'get_max':'channel_je_num_sum_get_max_x'}).\
    reset_index()
```

**SQL**

```hive
SELECT partyid_casedate, MAX(je_num_sum) AS channel_je_num_sum_get_max_x
FROM (
    SELECT partyid_casedate, channel, SUM(je_num) AS je_num_sum
    FROM res_raw_pd
    GROUP BY partyid_casedate, channel
) grouped_sum
GROUP BY partyid_casedate;
```

#### 出账-现转标志-交易金额

**python**

```python
# 出帐
raw_in_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==2]

#调用data_process_2，返回以‘客户号_案例日期’和‘现转标志’分组，出账‘折合人民币’的和
#即，我行客户在现金或转账中的出账总流水数据，以我行客户与现转标志分组
res_pd_temp_1=MyUtils.data_process_2(raw_in_pd,'cash_trans_flag','je_num',['sum'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’中现金或转账的出账‘折合人民币的和’的最大值
#即，我行客户在现金或转账中的出账总流水数据中，金额最大的数据
res_pd_1_5=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_sum':[BasicFunc.get_max]}).loc(axis=1)['je_num_sum'].\
    rename(columns={'get_max':'cash_trans_flag_je_num_sum_get_max_y'}).\
    reset_index()
```

**SQL**

```hive
SELECT partyid_casedate, MAX(je_num_sum) AS cash_trans_flag_je_num_sum_get_max_y
FROM (
    SELECT partyid_cased, cash_trans_flag, SUM(je_num) AS je_num_sum
    FROM res_raw_pd
    WHERE receive_pay_cd = 2
    GROUP BY partyid_casedate, cash_trans_flag
) grouped_sum
GROUP BY partyid_casedate;
```

#### 全量-现转标志-交易金额

**python**

```python
#调用data_process_2，返回以‘客户号_案例日期’和‘现转标志’分组，全量‘折合人民币’的和
#即，我行客户在现金或转账中的总流水数据，以我行客户与现转标志分组
res_pd_temp_1=MyUtils.data_process_2(res_raw_pd,'cash_trans_flag','je_num',['sum'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’中现金或转账的全量‘折合人民币的和’的最大值
#即，我行客户在现金或转账的总流水数据中，金额最大的数据
res_pd_1_6=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_sum':[BasicFunc.get_max]}).loc(axis=1)['je_num_sum'].\
    rename(columns={'get_max':'cash_trans_flag_je_num_sum_get_max_x'}).\
    reset_index()
```

**SQL**

```hive
SELECT partyid_casedate, MAX(je_num_sum) AS cash_trans_flag_je_num_sum_get_max_x
FROM (
    SELECT partyid_cased, cash_trans_flag, SUM(je_num) AS je_num_sum
    FROM res_raw_pd
    GROUP BY partyid_casedate, cash_trans_flag
) grouped_sum
GROUP BY partyid_casedate;
```

### res_pd_6 & res_pd_7

#### res_raw_pd

```python
#提取，客户号_案例日期；收付标志；对方行号；折合人民币；余额；对方是否我行客户
res_raw_pd=data[['partyid_casedate','receive_pay_cd','opp_acct_num','cnt_amt','amt_val','opp_isparty']]
res_raw_pd['je_num']=res_raw_pd['cnt_amt'].astype(float)
res_raw_pd['zhye_num']=res_raw_pd['amt_val'].astype(float)
```

#### 全量-对方行号-交易次数

**python**

```python
#调用data_process_2，返回以‘客户号_案例日期’和‘对方行号’分组，交易次数
#即，我行客户与对方行号的总交易次数
res_pd_temp_1=MyUtils.data_process_2(res_raw_pd,'opp_acct_num','je_num',['count'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’和‘对方行号’交易次数的25%分位与中位数
#即，我行客户与对方行号交易次数的1/4位和中位数数据，交易次数升序排序
res_pd_6_1=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_count':[BasicFunc.get_25_per,BasicFunc.get_50_per]}).\
	loc(axis=1)['je_num_count'].\
    rename(columns={'get_25_per':'dfzh_je_num_count_get_25_per_x',\
                    'get_50_per':'dfzh_je_num_count_get_50_per_x'}).\
    reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	PERCENTILE(je_num_count, 0.25) AS dfzh_je_num_get_25_per_x, 
	PERCENTILE(je_num_count, 0.5) AS dfzh_je_num_count_get_50_per_x
FROM (
    SELECT partyid_cased, opp_acct_num, COUNT(*) AS je_num_count
    FROM res_raw_pd
    GROUP BY partyid_casedate, opp_acct_num
) grouped_count
GROUP BY partyid_casedate;
```

#### 出账-对方行号-交易次数

**python**

```python
#出账
raw_out_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==2]

#调用data_process_2，返回以‘客户号_案例日期’和‘对方行号’分组，出账交易次数
#即，我行客户与对方行号的出账交易次数
res_pd_temp_1=MyUtils.data_process_2(raw_out_pd,'opp_acct_num','je_num',['count'])

#以‘客户号_案例日期’分组，得到每个‘客户号_案例日期’和‘对方行号’出账交易次数的25%分位与中位数
#即，我行客户与对方行号出账交易次数的1/4位和中位数数据，交易次数升序排序
res_pd_6_2=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num_count':[BasicFunc.get_50_per]}).loc(axis=1)['je_num_count'].\
    rename(columns={'get_50_per':'dfzh_je_num_count_get_50_per'}).\
    reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	PERCENTILE(je_num_count, 0.5) AS dfzh_je_num_count_get50_per
FROM (
    SELECT partyid_cased, opp_acct_num, COUNT(*) AS je_num_count
    FROM res_raw_pd
    WHERE receive_pay_cd = 2
    GROUP BY partyid_casedate, opp_acct_num
) grouped_count
GROUP BY partyid_casedate;
```

#### 入账-我行客户-交易金额

**python**

```python
#入账
raw_in_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==1]

#对方是我行客户
res_pd_temp_3=res_raw_pd[res_raw_pd['opp_isparty']==1]

#以‘客户号_案例日期’分组，每组交易金额的最大值与极差
res_pd_temp_4=res_pd_temp_3.groupby('partyid_casedate').\
    agg({'zhye_num':[BasicFunc.get_kur],'je_num':[BasicFunc.get_max,BasicFunc.get_max_gap]})
res_pd_7_4=res_pd_temp_4.loc(axis=1)['je_num'].\
	rename(columns={'get_amx':'wh_je_num_get_max_y',\
                    'get_mx_gap':'wh_je_num_get_max_gap_y'}).\
	reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	MAX(je_num) AS wh_je_num_get_max_y, 
	MAX(je_num) - MIN(je_num) AS wh_je_num_get_max_gap_y
FROM (
    SELECT partyid_cased, je_num, zhye_num
    FROM res_raw_pd
    WHERE opp_isparty = 1 AND receive_pay_cd = 1
) grouped_data
GROUP BY partyid_casedate;
```

#### 入账-非我行客户-余额&交易金额

**python**

```python
#对方非我行客户
res_pd_temp_5=raw_in_pd[raw_in_pd['opp_isparty']!=1]

#以‘客户号_案例日期’分组，每组余额的25%分位和交易金额的25%分位，升序
res_pd_temp_6=res_pd_temp_5.groupby('partyid_casedate').\
    agg({'zhye_num':[BasicFunc.get_25_per],'je_num':[BasicFunc.get_25_per]})

res_pd_7_5=res_pd_temp_6.loc(axis=1)['zhye_num'].\
	rename(columns={'get_25_per':'fwh_zhye_num_get_25_per_y'}).reset_index()
res_pd_7_6=res_pd_temp_6.loc(axis=1)['je_num'].\
	rename(columns={'get_25_per':'fwh_je_num_get_25_per_y'}).reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	PERCENTILE(zhye_num, 0.25) AS fwh_zhye_num_get_25_per_y
FROM (
    SELECT partyid_cased, zhye_num
    FROM res_raw_pd
    WHERE opp_isparty != 1 AND receive_pay_cd = 1
) grouped_data
GROUP BY partyid_casedate;

SELECT 
	partyid_casedate, 
	PERCENTILE(je_num, 0.25) AS fwh_je_num_get_25_per_y
FROM (
    SELECT partyid_cased, je_num
    FROM res_raw_pd
    WHERE opp_isparty != 1 AND receive_pay_cd = 1
) grouped_data
GROUP BY partyid_casedate;
```



#### 出账-我行客户-余额&交易金额

**python**

```python
#出账
raw_out_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==2]

#我行客户
res_pd_temp_7=raw_out_pd[raw_out_pd['opp_isparty']==1]

#以‘客户号_案例日期’分组，每组余额的峰度、交易金额的偏度和峰度
res_pd_temp_8=res_pd_temp_7.groupby('partyid_casedate').\
    agg({'zhye_num':[BasicFunc.get_kur],'je_num':[BasicFunc.get_skew,BasicFunc.get_kur]})  

res_pd_7_7=res_pd_temp_8.loc(axis=1)['zhye_num'].\
	rename(columns={'get_kur':'wh_zhye_num_get_kur'}).reset_index()
res_pd_7_8=res_pd_temp_8.loc(axis=1)['je_num'].\
	rename(columns={'get_skew':'wh_je_num_get_skew','get_kur':'wh_je_num_get_kur'}).reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	KURTOSIS(zhye_num) AS wh_zhye_get_kur
FROM (
    SELECT partyid_cased, zhye_num
    FROM res_raw_pd
    WHERE opp_isparty = 1 AND receive_pay_cd = 2
) grouped_data
GROUP BY partyid_casedate;

SELECT
	partyid_casedate, 
	SKEWNESS(je_num) AS wh_je_num_get_skew, 
	KURTOSIS(je_num) AS wh_je_num_get_kur
FROM (
    SELECT partyid_cased, je_num
    FROM res_raw_pd
    WHERE opp_isparty = 1 AND receive_pay_cd = 2
) grouped_data
 BY partyid_casedate;
```

#### 出账-非我行客户-余额&交易金额

**python**

```python
#非我行客户
res_pd_temp_9=raw_out_pd[raw_out_pd['opp_isparty']!=1]

#以‘客户号_案例日期’分组，每组交易金额的75%分位、75%分位与25%分位差值、均值
res_pd_temp_10=res_pd_temp_9.groupby('partyid_casedate').\
    agg({'je_num':[BasicFunc.get_75_per,BasicFunc.get_4_dis,BasicFunc.get_avg]})
        
res_pd_7_9=res_pd_temp_10.loc(axis=1)['je_num'].\
    rename(columns={'get_75_per':'fwh_je_num_get_75_per',\
                    'get_4_dis':'fwh_je_num_get_4_dis',\
                    'get_avg':'fwh_je_num_get_avg'}).\
    reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	PERCENTILE(je_num, 0.75) AS fwh_je_num_get_75_per, 
    PERCENTILE(je_num, 0.75) - PERCENTILE(j_num, 0.25) AS fwh_je_num_get_4_dis, 
    AVG(je_num) AS fwh_je_num_get_avg
FROM (
    SELECT partyid_cased, je_num
    FROM res_raw_pd
    WHERE opp_isparty != 1 AND receive_pay_cd = 2
) grouped_data
GROUP
```



### res_pd_8

#### res_raw_pd

```python
#提取，客户号_案例日期；收付标志；对方客户类型；折合人民币；余额
res_raw_pd=data[['partyid_casedate', 'receive_pay_cd', 'opp_party_class_cd', 'cnt_amt','amt_val']]
res_raw_pd['je_num']=res_raw_pd['cnt_amt'].astype(float)
res_raw_pd['zhye_num']=res_raw_pd['amt_val'].astype(float)
```

#### 出账-对公客户-交易金额

**python**

```python
#出账
raw_out_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==2]

#对公客户
res_pd_temp_1=raw_out_pd[raw_out_pd['opp_party_class_cd'].isin(['C'])]

#以‘客户号_案例日期’分组，每组交易金额的最大值、75%分位与25%分位差值
res_pd_8_1=res_pd_temp_1.groupby('partyid_casedate').\
    agg({'je_num':[BasicFunc.get_max,BasicFunc.get_4_dis]}).loc(axis=1)['je_num'].\
    rename(columns={'get_max':'dg_je_num_get_max','get_4_dis':'dg_je_num_get_4_dis'}).\
    reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	MAX(je_num) AS dg_je_num_get_max, 
    PERCENTILE(je_num, 0.75) - PERCENTILE(je_num, 0.25) AS dg_je_num_get_4_dis
FROM (
    SELECT partyid_cased, je_num
    FROM res_raw_pd
    WHERE receive_pay_cd = 2 AND opp_party_class_cd = 'C'
) grouped_data
GROUP BY partyid_casedate;
```

#### 入账-对公客户-交易金额

**python**

```python
#入账
raw_in_pd=res_raw_pd[res_raw_pd['receive_pay_cd']==1]

# 对公客户
res_pd_temp_4=raw_in_pd[raw_in_pd['opp_party_class_cd'].isin(['C'])]

#以‘客户号_案例日期’分组，每组交易金额的最大值与极差
res_pd_8_4=res_pd_temp_4.groupby('partyid_casedate').\
    agg({'je_num':[BasicFunc.get_max,BasicFunc.get_max_gap]}).loc(axis=1)['je_num'].\
    rename(columns={'get_max':'dg_je_num_get_max_y','get_max_gap':'dg_je_num_get_max_gap_y'}).\
    reset_index()
```

**SQL**

```hive
SELECT 
	partyid_casedate, 
	MAX(je_num) AS dg_je_num_get_max_y, 
    MAX(je_num) - MIN(je_num) AS dg_je_num_get_gap_y
FROM (
    SELECT partyid_cased, je_num
    FROM res_raw_pd
    WHERE receive_pay_cd = 1 AND opp_party_class_cd = 'C'
) grouped_data
GROUP BY partyid_casedate;
```

