#Map集合  
##基本特点
Map：一次添加一对元素。Collection：一次添加一个元素  
Map也成为双列集合，Collection集合称为单列集合。  
其实Map集合中存储的就是键值对。  
Map集合中必须保证键的唯一性。

##常用方法  
1. 添加  
value put (key,value)：返回前一个和key关联的值，如果没有返回null。
2. 删除  
void clear（)：清空Map集合  
value remove（key）:根据指定的key返回这个键值对。  
3. 判断  
boolean containsKey(key):包含键吗？  
boolean containsValue(value):包含值吗？  
boolean isEmpty(); 有键值对吗？
4. 获取  
value get(key):通过键获取值，如果没有该键返回null  
当然可以通过返回null，来判断是否包含指定键  
int size():获取键值对的个数  

##Map常用的子类  
> * Hashtable:内部结构是哈希表，是同步的。不允许null作为键，null作为值。  
> > Properties:用来存储键值对型的配置文件的信息，可以和IO技术相结合。  
  
> * HashMap:内部结构是哈希表，不是同步的。允许null作为键，null作为值。
  
> * TreeMap:内部结构是二叉树，不是同步的。可以对Map集合中的键进行排序。
