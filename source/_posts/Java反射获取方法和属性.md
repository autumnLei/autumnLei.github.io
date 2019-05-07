---
title: Java反射获取方法和属性
date: 2019-05-02 00:53:04
tags:
- Java
---
获取方法
getMethod（String name, Class<?>... parameterTypes) 返回类中的某个公共方法-包括父类中的公共方法
getMethods（）返回方法数组 下同
getDeclaredMethod（String name, Class<?>... parameterTypes) 返回类中所有方法-包括私有方法<!-- more -->

获取属性
getField(String name) 返回pubilc属性- 包括父类
getFields() 返回属性数组 下同
getDeclaredField（String name）返回所有属性-不包括父类
```
//default包中的Test03类
public class Test03 {
    
    public String a = "Test03的public字符串";
    
    private String b = "Test03的private字符串";
    
    public void show(){
        System.out.println("hi huang!");
    }
    private void show_private(){
        System.out.println("this is a peivate method");
    }
    public void show(String str, String s){
        System.out.println(str+s);
    }
}
```

```
package lei;
 
import java.lang.reflect.Field;
import java.lang.reflect.Method;
 
 
public class Test04 {
     
    public static void main(String[] args){
 
      //用反射调用default包里的Test03类中的无参show方法 第二个参数也可以写null 这里空着
        try{
            Class<?> c = Class.forName("Test03");
            Method method = c.getMethod("show");
            System.out.println(method);
            method.invoke(c.newInstance());
        }catch(Exception e){
             
        }
         
      //用反射调用default包里的Test03类中的参数为两个String的show方法
        try{
            Class<?> c = Class.forName("Test03");
            Method method = c.getMethod("show", String.class, String.class);
            System.out.println(method);
            //String[] varArgs = {"shixin", "zhang"};
            method.invoke(c.newInstance(), "hi men", "!!!");
        }catch(Exception e){
             
        }
         
      //调用Test03中的私有无参方法
        try{
            Class<?> c = Class.forName("Test03");
            Method method = c.getDeclaredMethod("show_private");
            method.setAccessible(true);
            System.out.println(method);
            method.invoke(c.newInstance());
        }catch(Exception e){
             
        }
         
      //反射实例化Test03类 打印Test03中所有属性
        try{
            Class<?> c = Class.forName("Test03");
            Field[] fields = c.getDeclaredFields();
             
            Field f = c.getField("a");
            System.out.println(f.get(new T()));
             
            for (Field field: fields){
                field.setAccessible(true);
                System.out.println(field);
                System.out.println(field.get(new T()));
            }          
        }catch(Exception e){
             
        }
         
        //反射实例化Test03类 打印Test03中私有属性
        try{
            Class<?> c = Class.forName("Test03");
            Field f = c.getDeclaredField("b");
             
            f.setAccessible(true);
            System.out.println(f);
            System.out.println(f.get(c.newInstance()));        
        }catch(Exception e){
             
        }
    }
 
}

```
