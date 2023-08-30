---
title: 黑马顺义校区Java生产实训 day3 笔记
description: 
published: true
date: 2023-08-30T02:06:45.934Z
tags: 北京, 黑马
editor: markdown
dateCreated: 2023-08-30T01:15:08.373Z
---

昨天特地安装了wiki.js

# 1.SpringBoot整合Mybatis完成表单CRUD

## 1.1 REST风格
强调以资源为中心，在URL中部出现动词；通过post、put、get、delete请求来区分是保存、修改、查询、删除操作。

## 1.2 @ResquestMapping子注解
`` @GetMapping`` GET请求。同理其余请求。

``@RestController``
## 1.3 以访问Student数据为例的代码
项目目录结构：
![](https://qiniu.oos.hailay.site/img/20230830100123.png)

site/hailay/controller/StudentController.java
```java
@RestController // 声明当前类可以接受请求和生成响应，并且生成类对象放入SpringIOC中
public class StudentController {

    @Autowired
    private StudentService studentService;

    // get请求查询所有学生对象
    @GetMapping("students")
    public List<Student> findAll(){
        List<Student> list = studentService.findAll();
        return list;
    }

    // 处理get请求根据id查询单条记录，id是动态时用{}作为占位符
    @GetMapping("students/{sid}")
    // @PathVariable(value = "sid") 获取路径中大括号的参数，赋值给方法的形参
    // 如果sid和id的名称一致，则(value = "sid")可以省略
    public Student findById(@PathVariable(value = "sid") Long id){
        Student student = studentService.findById(id);
        return student;
    }
}

```
site/hailay/mapper/StudentMapper.java
```java
@Mapper // 底层会创建该接口的代理对象，将其放入spring IOC 容器中
public interface StudentMapper {
    @Select("select * from tb_student where id = #{id}")
    Student findById(Long id);

    @Select("select * from tb_student")
    List<Student> findAll();

    @Insert("insert into tb_student values(null, #{number},#{userName}, #{birthday}, #{address})")
    // 返回新增记录的主键，设置给参数的id属性
    @Options(useGeneratedKeys = true, keyProperty = "id")
    void save(Student student);

    @Update("update tb_student set number=#{number}, user_name=#{userName}, birthday=#{birthday}, address=#{address} where id=#{id}")
    void updateById(Student student);

    @Delete("delete from tb_student where id=#{id}")
    void deleteById(Long id);

    // 条件查询，动态sql建议写在xml文件中
    List<Student> findByCondition(String username, String num);
}
```
site/hailay/service/StudentService.java
```java
public interface StudentService {
    List<Student> findAll();

    Student findById(Long id);
}

```
site/hailay/service/impl/StudentServiceImpl.java
```java
@Service // 在项目启动的时候创建该类的对象，放入Spring IOC中
public class StudentServiceImpl implements StudentService {

    @Autowired // 从容器中获取指定类型的对象，设置给当时的成员变量。
    private StudentMapper studentMapper;

    @Override
    public List<Student> findAll() {
        List<Student> list = studentMapper.findAll();
        return list;
    }

    @Override
    public Student findById(Long id) {
        return studentMapper.findById(id);
    }
}
```
site/hailay/mapper/StudentMapper.xml
````xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="site.hailay.mapper.StudentMapper">


    <select id="findByCondition" resultType="site.hailay.pojo.Student">
        select * from tb_student
--         where标签会自动去掉多余的and / or 和条件满足则带where
        <where>
            <if test="username != null and username !='' ">
                and user_name like concat('%',#{username},'%')
            </if>
            <if test="num != null and num != '' ">
                and number = #{num}
            </if>
        </where>
    </select>
</mapper>
````

### 效果
http://localhost:9090/students

#### 获取全部
![](https://qiniu.oos.hailay.site/img/20230830095530.png)

#### 获取单个用户

![](https://qiniu.oos.hailay.site/img/20230830095752.png)


