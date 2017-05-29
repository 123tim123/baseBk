---
title: gson   
tags: [android,数据]
---

# gson基本使用
> gson json使用最频繁的工具在这里就不做过多的介绍只介绍一下基本使用和几个注意点

### tojson将java对象转换为json
```
Gson gson = new Gson(); 
List persons = new ArrayList(); 
String str = gson.toJson(persons);
```
### fromjson将json字符串转换为实体对象
```
Gson提供了fromJson()方法来实现从Json字符串转化为到java实体的方法。 
比如json字符串为：[{“name”:”name0”,”age”:0}] 
Person person = gson.fromJson(str, Person.class); 
```

### 注意点在使用泛型转换时
```
class GsonResponsePasare<T> implements ParameterizedType {
        private final UtilsLog lg = UtilsLog.getLogger(GsonResponsePasare.class);

        public T deal(String response) {
//            Type gsonType = new ParameterizedType() {//...};//不建议该方式，推荐采用GsonResponsePasare实现ParameterizedType.因为getActualTypeArguments这里涉及获取GsonResponsePasare的泛型集合
            Type gsonType = this;

            CommonResponse<T> commonResponse = new Gson().fromJson(response, gsonType);
            lg.e("Data is : " + commonResponse.data, "Class Type is : " + commonResponse.data.getClass().toString());
            return commonResponse.data;
        }

        @Override
        public Type[] getActualTypeArguments() {
            Class clz = this.getClass();
            //这里必须注意在外面使用new GsonResponsePasare<GsonResponsePasare.DataInfo>(){};实例化时必须带上{},否则获取到的superclass为Object
            Type superclass = clz.getGenericSuperclass(); //getGenericSuperclass()获得带有泛型的父类
            if (superclass instanceof Class) {
                throw new RuntimeException("Missing type parameter.");
            }
            ParameterizedType parameterized = (ParameterizedType) superclass;
            return parameterized.getActualTypeArguments();
        }

        @Override
        public Type getOwnerType() {
            return null;
        }

        @Override
        public Type getRawType() {
            return CommonResponse.class;
        }
    }
```

## GsonBuilder基本配置
> Gson g = new GsonBuilder().serializeNulls().setDateFormat("yyyy-MM-dd hh:mm").create();
> serializeNulls 设置此项对象为空也转换  
> setDateFormat 将时间类型转换为对应格式的字符串