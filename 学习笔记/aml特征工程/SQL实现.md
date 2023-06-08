#### 指定二类分组聚合

```python
def data_process_2(raw_pd:pd.DataFrame,col_name:str,sum_col:str,base_func_list:list):
        """根据指定的col_name 进行过滤，然后根据客户号partyid_casedate与指定col_name进行分组

        Args:
            raw_pd (pd.DataFrame): 客户号，指定col_name以及sunm_col
            col_name (str): 指定的列名
            sum_col (str): 指定的统计列
            base_func_list (list): 对统计列进行统计的函数
            return(pd.DataFrame)	
        """
        raw_pd_1 = raw_pd[['partyid_casedate',col_name,sum_col]]
        raw_filter_pd = raw_pd_1[(raw_pd_1[col_name] != ' ')&(raw_pd_1[col_name].notna())] 
        res_pd_temp = raw_filter_pd.groupby(['partyid_casedate',col_name]).agg({sum_col:base_func_list})
        rename_dict ={}
        for i in base_func_list:
            rename_dict[i]=sum_col+'_'+i
        res_pd = res_pd_temp.loc(axis=1)[sum_col].rename(columns=rename_dict).reset_index()
        return res_pd
```

为了使用Hive SQL实现上述Python代码的功能，我们需要将数据处理过程分解为几个步骤。以下是使用Hive SQL实现的等效代码：

1. 创建一个临时表，包含所需的列（partyid_casedate、col_name、sum_col）：

```hive
CREATE TEMPORARY TABLE temp_table AS
SELECT partyid_casedate, col_name, sum_col
FROM raw_table
WHERE col_name != ' ' AND col_name IS NOT NULL;
```

2. 使用GROUP BY和聚合函数对数据进行分组和统计：

```sql
CREATE TEMPORARY TABLE result_table AS
SELECT partyid_casedate, col_name,
       MIN(sum_col) AS sum_col_min,
       MAX(sum_col) AS sum_col_max,
       AVG(sum_col) AS sum_col_avg,
       SUM(sum_col) AS sum_col_sum
FROM temp_table
GROUP BY partyid_casedate, col_name;
```

请注意，您需要将`raw_table`替换为您的原始数据表名称，将`col_name`和`sum_col`替换为实际列名。同时，根据`base_func_list`中的聚合函数，您需要在第二个SQL查询中添加或删除相应的聚合函数。

最后，您可以使用以下查询从`result_table`中检索结果：

```hive
SELECT * FROM result_table;
```

这将返回一个与Python代码功能相同的结果集。