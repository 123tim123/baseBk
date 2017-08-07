---
title: dqdp sql 再次封装
tags: [java]
---
## 工具类代码 
```
public class SQLUtil {
    public static String getCountSQL(String searchSQL) {
        return "select count(1) from (" + searchSQL.replaceAll("(?i)\\basc\\b|\\bdesc\\b", "").replaceAll("(?i)order\\s+by\\s+\\S+(\\s*[,\\s*\\S+])*", "") + "  ) a ";
    }

    /**
     * 拦截搜索内容
     * @param object
     * @return
     */
    public static Object intercept(Object object) {
        if (object == null) return object;
        if (object instanceof String) {
            String str = (String) object;
            return str.replaceAll("([';])+|(--)+","");
        }
        return null;
    }

    /**
     * 分页查询
     *
     * @param baseDAO
     * @param condSQL
     * @param cls
     * @param searchValue
     * @param pager
     * @return
     * @throws SQLException
     */
    public static Pager pagetSql(BaseDAOImpl baseDAO, String condSQL, Class cls, Map searchValue, Pager pager) throws SQLException {
        String countSQL = querySize(condSQL);
        String searchSQL = condSQL;
        return baseDAO.pageSearchByField(cls, countSQL, searchSQL, searchValue, pager);
    }

    public static String querySize(String sql) throws SQLException {
        String f = "FROM";
        String basesql = "select count(1) FROM ";
        int index = sql.indexOf(f);
        String akk = basesql + sql.substring(index + f.length(), sql.length());
        return akk;
    }

    public static class ABean {
        public String A;
        public String B = "bbb";

        public ABean(String A) {
            this.A = A;
        }
    }

    public static void main(String[] arg) throws IllegalAccessException {

        String sql = AddSearch.create("insert ").insert(new ABean("aa"), "A", "B").build();
        System.out.println(sql);
    }

    public static class AddSearch {
        Map<String, Object> map;
        String sql;
        private int type;

        public String build() {
            return sql;
        }

        public AddSearch(Map<String, Object> map, String sql) {
            this.map = map;
            this.sql = sql;
            this.type = 0;
        }

        public AddSearch(String sql) {
            this.sql = sql;
            this.type = 1;
        }

        public static AddSearch create(Map<String, Object> map, String sql) {
            return new AddSearch(map, sql);
        }

        public static AddSearch create(String sql) {
            return new AddSearch(sql);
        }

        public AddSearch deleteList(String key, List<String> ids) {
            sql += " ( ";
            for (String str : ids) {
                String itemSql = " ";
                itemSql += key + " = '" + intercept(str) + "' or";
                sql += itemSql;
            }
            sql = VerifyUtil.endClearOr(sql);
            sql += " ) ";
            return this;
        }


        /**
         * 批量插入
         *
         * @param list
         * @param <T>
         * @return
         * @throws IllegalAccessException
         */
        public <T> AddSearch insertList(List<T> list, String... keys) throws IllegalAccessException {
            sql += " values ";
            for (T item : list) {
                sql += " ( ";
                Class userCla = (Class) item.getClass();
                Field[] fs = userCla.getDeclaredFields();
                Map<String, Object> map = new HashMap<>();
                for (Field f : fs) {
                    f.setAccessible(true);
                    Object val = f.get(item);
                    map.put(f.getName(), val);
                }
                String itemSql = "";
                for (String key : keys) {
                    String val = map.get(key) == null ? null : "'" + intercept(map.get(key)) + "'";
                    itemSql += val + ",";
                }
                itemSql = VerifyUtil.endClear(itemSql);
                sql += itemSql;
                sql += " ),";
            }
            sql = VerifyUtil.endClear(sql);
            return this;
        }

        /**
         * 插入数据
         *
         * @return
         * @throws IllegalAccessException
         */
        public <T> AddSearch insert(T t, String... keys) throws IllegalAccessException {
            sql += " values ( ";
            Class userCla = (Class) t.getClass();
            Field[] fs = userCla.getDeclaredFields();
            Map<String, Object> map = new HashMap<>();
            for (Field f : fs) {
                f.setAccessible(true);
                Object val = f.get(t);
                map.put(f.getName(), val);
            }
            String itemSql = "";
            for (String key : keys) {
                String val = map.get(key) == null ? null : "'" + intercept(map.get(key)) + "'";
                itemSql += val + ",";
            }
            itemSql = VerifyUtil.endClear(itemSql);
            sql += itemSql;
            sql += " )";
            return this;
        }

        /**
         * @param sqlName 条件
         * @param mapKey
         * @return
         */
        public AddSearch add(String sqlName, String mapKey) {
            if (type == 0) {
                addMap(sqlName, mapKey);
            } else {
                addValue(sqlName, mapKey);
            }
            return this;
        }

        /**
         * @param sqlName
         * @param mapKey
         * @return
         */
        public AddSearch addLike(String sqlName, String mapKey) {
            if (type == 0) {
                addLikeMap(sqlName, mapKey);
            } else {
                addLikeValue(sqlName, mapKey);
            }
            return this;
        }

        /**
         * 查询in方法
         *
         * @param sqlName
         * @param values
         * @return
         */
        public AddSearch in(String sqlName, List<String> values) {
            String inSql = "";
            for (String str : values) {
                if (!VerifyUtil.isEmpty(str)) {
                    inSql += "'" + intercept(str) + "',";
                }
            }
            inSql = VerifyUtil.endClear(inSql);
            sql += " and " + sqlName + " in ( " + inSql + " ) ";
            return this;
        }


        /**
         * map中赋值
         *
         * @param sqlName
         * @param mapKey
         */
        private void addMap(String sqlName, String mapKey) {
            if (!VerifyUtil.isEmpty(map.get(mapKey)))
                sql += " and " + sqlName + " = '" + intercept(map.get(mapKey)) + "'";
        }

        private void addLikeMap(String sqlName, String mapKey) {
            if (!VerifyUtil.isEmpty(map.get(mapKey)))
                sql += " and " + sqlName + " like '%" + intercept(map.get(mapKey)) + "%'";
        }

        /**
         * value直接赋值
         *
         * @param sqlName
         * @param mapKey
         */
        private void addValue(String sqlName, String mapKey) {
            if (!VerifyUtil.isEmpty(mapKey))
                sql += " and " + sqlName + " = '" + intercept(mapKey) + "'";
        }

        private void addLikeValue(String sqlName, String mapKey) {
            if (!VerifyUtil.isEmpty(mapKey))
                sql += " and " + sqlName + " like '%" + intercept(mapKey) + "%'";
        }

    }
}


```


### 批量插入vo对象
```
 @Override
    public void insertAnswerTopic(List<AnswerDetailVO> answerDetailVO) throws Exception, BaseException {
        if (answerDetailVO.size() < 1)
            return;
        String[] keys = {"answerDetailId", "answerId", "questionnaireQuestionId",
                "questionClassify", "questionnaireLabelId", "inputContent"};
        String sql = "INSERT INTO tb_wj_answer_detail" +
                " (`answer_detail_id`, `answer_id`, `questionnaire_question_id`," +
                " `question_classify`, `questionnaire_label_id`, `label_name`, " +
                "`questionnaire_option_id`, `option_name`, `input_content`)";
        sql = SQLUtil.AddSearch.create(sql).insertList(answerDetailVO, keys).build();
        super.preparedSql(sql);
        super.executeUpdate();
    }

```
> keys 则是对象的属性，是通过反射获取需要插入对象内属性，再进行插入.  
> 注意顺序与语句sql一致

### 分页查询
```
 @Override
    public Pager searchAreaTemplateStatus(Map searchValue, Pager pager) throws Exception,SQLException {
        String sql ="SELECT t.* FROM tb_wj_area_template t where 1=1 ";
        sql= SQLUtil.AddSearch.create(searchValue,sql)
                .add("t.questionnaire_type","type").build();
        return SQLUtil.pagetSql(this,sql, AreaTemplateVO.class,searchValue,pager);
    }

```
> 分页查询在dqdp封装中的 pager内进行再次封装
> 如使用basetable 2.0 翻页方法 在SQLUtil 中添加一个pagetSql对应的basetable即可
> add 则添加查询语句addLike 为模糊搜索
> 如传入map add和addLike 第二个值传入map对象的key值   
> 如没有传map add和addlike 第二个值则传入sql该字段的值

### 多条数据查询批量查询
```
 @Override
    public List<TemplateQuestionBankVO> findTopicByTemplateQuestionIds(List<String> qustIds) throws Exception,SQLException{
        if(qustIds.size()<1)
            return new ArrayList<>();
        String sql ="SELECT t.* from tb_wj_template_question_bank t where 1=1 ";
        sql= SQLUtil.AddSearch.create(sql)
                .in("t.template_question_id",qustIds)
                .build();
        return super.getList(sql,TemplateQuestionBankVO.class);
    }

```

### 多条数据删除
```
  @Override
    public void deleteTemplateList(List<String> ids) throws Exception, SQLException {
        if(ids.size()<1)
            return;
        String sql ="DELETE FROM tb_wj_template_question_bank WHERE ";
        sql =SQLUtil.AddSearch.create(sql).deleteList("template_question_id",ids).build();
        System.out.println(sql);
        super.preparedSql(sql);
        super.executeUpdate();
    }

```

