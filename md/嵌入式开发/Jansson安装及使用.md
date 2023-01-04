# Jansson安装及使用

## 安装

```bash
wget http://www.digip.org/jansson/releases/jansson-2.10.tar.gz

tar -zxvf jansson-2.10.tar.gz

cd jansson-2.10

./configure

make

sudo make install
```

## 使用

官方API手册：<https://jansson.readthedocs.io/en/latest/apiref.html>



### 解析流程

1. 使用`json_loads`加载json数据

2. 使用`json_object_get`通过键值获取对象指针

3. 使用`json_integer_value`、`json_string_value`、`json_array_get`获取对象的值

4. 使用`json_decref`释放资源

### 构建流程

1. 使用`json_object`创建根对象

2. 使用`json_array`、`json_string`、`json_integer`等函数创建下一级对象

3. 使用`json_object_set_new`将对象放在上一级对象中

4. 使用`json_dumps`转换成字符串，用完后使用`free`释放字符串空间

5. 使用`json_decref`释放资源

### Demo

```c
#include <jansson.h>
#include <string.h>
#include <stdio.h>

const char *text = "{\"status\": true,\n\
\"person\": {\"alive\": true,\n\
\"name\": \"gulugulu\",\n\
\"age\": 20},\n\
\"people\":[{\"alive\": true,\n\
\"name\": \"hhh\",\n\
\"age\": 20},\n\
{\"alive\": false,\n\
\"name\": \"hellou\",\n\
\"age\": 80}]}";


int json_decode(void)
{
    json_error_t error;
    json_t *root;
    
    char *name;
    int age;
    
    root = json_loads((const char*)text, 0, &error); 
    if( !root )
    {
        printf("root format error:%d-%s\r\n", error.line, error.text);
        return -1;
    }

    printf("type of root:%d\n",json_typeof(root));
    
    json_t *status = json_object_get(root, "status");
    if(json_is_boolean(status))
    {
        printf("type of status:%d\n",json_typeof(status));
        if(json_is_true(status))
            printf("status:true\n");
        else 
            printf("status:false\n");
    }
    
    json_t *person = json_object_get(root, "person");
    if(json_is_object(person))
    {
        if(json_is_true(json_object_get(person, "alive")))
            printf("alive:true,");
        else if(json_is_false(json_object_get(person, "alive")))
            printf("alive:false,");

        name = (char*)json_string_value(json_object_get(person, "name"));
        age = json_integer_value(json_object_get(person, "age"));
        printf("name:%s,age:%d\n",name,age);
    }

    json_t *people = json_object_get(root, "people");
    if(json_is_array(people))
    {   
        json_t *one_people = NULL;
        int count = json_array_size(people);
        for(int idx = 0; idx < count; idx++)
        {
            one_people = json_array_get(people, idx);
            name = (char *)json_string_value(json_object_get(one_people, "name"));
            age = json_integer_value(json_object_get(one_people, "age"));
            printf("name:%s,age:%d\n",name,age);
        }

    }

    json_decref(root);
    return 0;
}

int json_encode()
{
    json_t *root;

    root = json_object();
    if(!root)
    {
        printf("create root failed\n");
        return -1;
    }

    json_object_set_new(root, "status", json_true());
    json_t *person = json_object();
    if(json_is_object(person))
    {
        json_object_set_new(root, "person", person);

        json_object_set_new(person, "alive", json_true());
        json_object_set_new(person, "name", json_string("gulugulu"));
        json_object_set_new(person, "age", json_integer(20));
    }

    json_t *people = json_array();
    json_t *one_people = json_object();

    if(json_is_array(people) && json_is_object(one_people))
    {
        json_object_set_new(root, "people", people);

        json_object_set_new(one_people, "alive", json_true());
        json_object_set_new(one_people, "name", json_string("hhh"));
        json_object_set_new(one_people, "age", json_integer(20));
        json_array_insert_new(people, 0, one_people);
        
        json_object_set_new(one_people, "alive", json_false());
        json_object_set_new(one_people, "name", json_string("hello"));
        json_object_set_new(one_people, "age", json_integer(80));
        json_array_insert_new(people, 1, one_people);

    }

    char *str = json_dumps(root, JSON_COMPACT);
    printf("ENCODE:%s\n",str);
    free(str);
    
    json_decref(root);
    return 0;
}

int main()
{
    json_encode();
    json_decode();
    return 0;
}   


```




