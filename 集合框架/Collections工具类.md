#Collections类

##方法

```
static <T extends Comparable<? super T>> void sort(List<T> list)
```
 根据元素的自然顺序 对指定列表按升序进行排序  

```
static <T> void sort(List<T> list,Comparator<? super T> c)
```

###最值  
Max() 两种，值传递和比较器传递  
Min() 同上

###折半方法  
binarySearch()

###逆序：逆转自然排序的顺序  
reverseOrder()

###替换  
replaceAll(List<T > list,T oldVal,T newVal)  

fill(List<? super T> list, T obj) 将所有元素都替换成obj

###随机排序  
static void shuffle(List<?> list)  
static void shuffle(List<?> list,Random rnd)  
使用默认随机源对指定列表进行置换  
  
  
1. 老的转成新的  
static <T> ArrayList<T> list(Enumeration<T> e)  
返回一个数组列表，它按返回顺序包含指定枚举返回的元素 

2. 新的转成老的  
老代码中有可能只接受枚举类型，而无法接收集合，就需要将集合转换成枚举类型的对象  
static <T> Enumeration enumeration(Collection<T> c)  
返回一个指定collection上的枚举  

###提供线程安全的集合  
static <T> Collection<T> synchronizedCollection(Collection<T> c)  
返回指定collection支持的同步（线程安全的）collection
  
static <T> List<T> synchronizedList(List<T> list)  
返回指定列表支持的同步（线程安全的）列表  

static <k,v> Map<k,v> synchronizedMap(Map<k,v> m)  
返回指定映射支持的同步（线程安全的）映射


