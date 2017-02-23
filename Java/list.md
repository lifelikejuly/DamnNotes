# 问题
开发中经常会遇到数组转list，list转数组
```
String str[] = new String[]{"ss","ss","ss"};
List<String> list  = Arrays.asList(str);
list.add("aaa");
```
运行报错
```
Caused by: java.lang.UnsupportedOperationException
at java.util.AbstractList.add(AbstractList.java:404)
```
开始还疑惑怎么就报错啊，想想平时初始化list
```
List<String> str = new ArrayList<>();
```
看看List的源码
```
public interface List<E> extends Collection<E> {

    boolean isEmpty();
    boolean contains(Object o);
    Iterator<E> iterator();
    Object[] toArray();
    <T> T[] toArray(T[] a);
    boolean add(E e);
    boolean remove(Object o);
    boolean containsAll(Collection<?> c);
    boolean addAll(Collection<? extends E> c);   
    boolean addAll(int index, Collection<? extends E> c);
    boolean removeAll(Collection<?> c);
    ...//省略部分源码
}
```

List是个接口，ArrayList、LinkedList等都实现List接口的方法。
Arrays的asList方法
```
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

返回 ArrayList,此ArrayList非彼ArrayList,它是Arrays内部的ArrayList
```
private static class ArrayList<E> extends AbstractList<E>
        implements RandomAccess, java.io.Serializable
    {
        private static final long serialVersionUID = -2764017481108945198L;
        private final E[] a;

        ArrayList(E[] array) {
            a = Objects.requireNonNull(array);
        }

        @Override
        public int size() {
            return a.length;
        }

        @Override
        public Object[] toArray() {
            return a.clone();
        }

        @Override
        @SuppressWarnings("unchecked")
        public <T> T[] toArray(T[] a) {
            int size = size();
            if (a.length < size)
                return Arrays.copyOf(this.a, size,
                                     (Class<? extends T[]>) a.getClass());
            System.arraycopy(this.a, 0, a, 0, size);
            if (a.length > size)
                a[size] = null;
            return a;
        }
    ......
    //省略部分源码
    }
```  

可以看到Arrays.ArrayList是固定大小的List，E[] a是不可变的。不支持add方法，但可用set方法修改列表中某一个值。

## 解决办法

Arrays.ArrayList赋值给ArrayList
```
String str[] = new String[]{"ss","ss","ss"};
ArrayList<String> list  = new ArrayList<>(Arrays.asList(str));
list.add("aaa");
```