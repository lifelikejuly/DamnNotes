# 区别
1. Set<Map.Entry<K,V>> entrySet()  返回此映射中包含的映射关系的 set 视图
2. Set<K>              keySet()      返回此映射中包含的键的 set 视图

# EntrySet
```
HashMap<String,Integer> hashMap = new HashMap<>();
        Set<Map.Entry<String,Integer>> setMap = hashMap.entrySet();
        setMap.forEach(new Consumer<Map.Entry<String, Integer>>() {
            @Override
            public void accept(Map.Entry<String, Integer> stringIntegerEntry) {
                stringIntegerEntry.getValue();
                stringIntegerEntry.getKey();
            }
        });
        Iterator<Map.Entry<String,Integer>> entryIterator =  setMap.iterator();
        while (entryIterator.hasNext()){
            Map.Entry<String,Integer> stringIntegerEntry =  entryIterator.next();
            stringIntegerEntry.getKey();
            stringIntegerEntry.getValue();
        }
```
# KeySet
```
HashMap<String,Integer> hashMap = new HashMap<>();
        Set<String> set = hashMap.keySet();
        set.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                hashMap.get(s);
            }
        });

        Iterator<String> stringIterator = set.iterator();
        while (stringIterator.hasNext()){
            String key = stringIterator.next();
            hashMap.get(key);
        }
```
# 源码

## KeySet
```
private final class KeySet extends AbstractSet<K> {
        public Iterator<K> iterator() {
            return newKeyIterator();
        }
        public int size() {
            return size;
        }
        public boolean contains(Object o) {
            return containsKey(o);
        }
        public boolean remove(Object o) {
            return HashMap.this.removeEntryForKey(o) != null;
        }
        public void clear() {
            HashMap.this.clear();
        }
        public final Spliterator<K> spliterator() {
            return new KeySpliterator<>(HashMap.this, 0, -1, 0, 0);
        }
        public final void forEach(Consumer<? super K> action) {
            HashMapEntry<K,V>[] tab;
            if (action == null)
                throw new NullPointerException();
            if (size > 0 && (tab = table) != null) {
                int mc = modCount;
                for (int i = 0; i < tab.length; ++i) {
                    for (HashMapEntry<K,V> e = tab[i]; e != null; e = e.next) {
                        action.accept(e.key);
                        // Android-modified - this was outside of the loop, inconsistent with other
                        // collections
                        if (modCount != mc) {
                            throw new ConcurrentModificationException();
                        }
                    }
                }

            }
        }
    }
```

## EntrySet
```
private final class EntrySet extends AbstractSet<Map.Entry<K,V>> {
        public Iterator<Map.Entry<K,V>> iterator() {
            return newEntryIterator();
        }
        public boolean contains(Object o) {
            if (!(o instanceof Map.Entry))
                return false;
            Map.Entry<K,V> e = (Map.Entry<K,V>) o;
            Entry<K,V> candidate = getEntry(e.getKey());
            return candidate != null && candidate.equals(e);
        }
        public boolean remove(Object o) {
            return removeMapping(o) != null;
        }
        public int size() {
            return size;
        }
        public void clear() {
            HashMap.this.clear();
        }
        public final Spliterator<Map.Entry<K,V>> spliterator() {
            return new EntrySpliterator<>(HashMap.this, 0, -1, 0, 0);
        }
        public final void forEach(Consumer<? super Map.Entry<K,V>> action) {
            HashMapEntry<K,V>[] tab;
            if (action == null)
                throw new NullPointerException();
            if (size > 0 && (tab = table) != null) {
                int mc = modCount;
                for (int i = 0; i < tab.length; ++i) {
                    for (HashMapEntry<K,V> e = tab[i]; e != null; e = e.next) {
                        action.accept(e);
                        // Android-modified - this was outside of the loop, inconsistent with other
                        // collections
                        if (modCount != mc) {
                            throw new ConcurrentModificationException();
                        }
                    }
                }
            }
        }
    }
```

## 问题
当做遍历操作时使用entrySet()会比keySet()更高效,由于Hashmap的get()也是一个循环操作。因此使用keySet遍历集合其实是进行了两次，而entrySet只做一次遍历获取所有的key-value
```
final Entry<K,V> getEntry(Object key) {
        if (size == 0) {
            return null;
        }

        int hash = (key == null) ? 0 : sun.misc.Hashing.singleWordWangJenkinsHash(key);
        for (HashMapEntry<K,V> e = table[indexFor(hash, table.length)];
             e != null;
             e = e.next) {
            Object k;
            if (e.hash == hash &&
                ((k = e.key) == key || (key != null && key.equals(k))))
                return e;
        }
        return null;
    }
```
```
Map<String, String> maps = new HashMap<String, String>();   
  //用entrySet()   
  Iterator<Entry<String,String>> it = maps.entrySet().iterator();   
  while(it.hasNext()){   
   Map.Entry<String,String> m = it.next();   
   String key = m.getKey();
   String value= m.getValue(); 
  }   
  //用keySet() 
for(String m: maps.keySet()){   
   String key = m;
   String value= maps.get(m);
  }
```