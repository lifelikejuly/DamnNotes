
# DeepCopy
无论原型对象的成员变量是值类型还是引用类型，都将复制一份给拷贝对象，深拷贝将原型对象的所有引用对象也复制一份给拷贝对象。在Java语言中，如果需要实现深拷贝，可以通过覆盖Object类的clone()方法实现，也可以通过序列化(Serialization)等方式来实现。（如果引用类型里面还包含很多引用类型，或者内层引用类型的类里面又包含引用类型，使用clone方法就会很麻烦。这时我们可以用序列化的方式来实现对象的深克隆。）
```
public class Student implements Cloneable {

    public String name;
    public Integer age;
    public Professor professor;

    public Student(String name, Integer age, Professor professor) {
        super();
        this.name = name;
        this.age = age;
        this.professor = professor;
    }


    @Override
    public Object clone() {
        Object o = null;
        try {
            o = (Student) super.clone();       
        } catch (CloneNotSupportedException e) {
            System.out.println(e.toString());
        }
        o.professor = (Professor) professor.clone();
        return o;
    }
}
```

```
public class Student implements Serializable,Cloneable {

    public String name;
    public Integer age;
    public Professor professor;

    @Override
    protected Student clone() throws CloneNotSupportedException {
        return (Student)super.clone();
    }
    public Student deepClone() throws IOException,ClassNotFoundException{
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
        objectOutputStream.writeObject(this);
        ByteArrayInputStream inputStream = new ByteArrayInputStream(outputStream.toByteArray());
        ObjectInputStream objectInputStream = new ObjectInputStream(inputStream);
        return (Student)objectInputStream.readObject();

    }
}
```
# ShallowCopy
在浅拷贝中，如果原型对象的成员变量是值类型，将复制一份给拷贝对象；如果原型对象的成员变量是引用类型，则将引用对象的地址复制一份给拷贝对象，也就是说原型对象和拷贝对象的成员变量指向相同的内存地址。
简单来说，在浅拷贝中，当对象被复制时只复制它本身和其中包含的值类型的成员变量，而引用类型的成员对象并没有复制。
```
public class Student implements Cloneable {

    public String name;
    public Integer age;
    public Professor professor;

    public Student(String name, Integer age, Professor professor) {
        super();
        this.name = name;
        this.age = age;
        this.professor = professor;
    }


    @Override
    public Object clone() {
        Object o = null;
        try {
            o = (Student) super.clone();       
        } catch (CloneNotSupportedException e) {
            System.out.println(e.toString());
        }
        return o;
    }
}

```

# Reference
- http://www.cnblogs.com/Qian123/p/5710533.html#_label2
- https://www.cnblogs.com/qlwang/p/7889802.html



