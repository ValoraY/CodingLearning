# 数据结构与算法

## 时间复杂度

1.用**核心操作的执行次数**表示时间复杂度

2.三条规则：

1. 常数加法可以用加1代替
2. 代替后的计算式我们只取高阶项
3. 高阶项的系数不是1时可以用1代替

3.采用大O记法：

- 常数阶O(1)
- 线性阶O(n)
- 平方阶O(n^2)
- 立方阶O(n^3)
- 对数阶O(log n)        底数可以是2，3，4...当n很大时，这些函数的规模相似

4.涉及函数调用的时间复杂度

​	分别分析每一个方法的时间复杂度，（因为是程序是顺序执行），然后把各个方法的时间复杂度加总，再依据3条规则修正，得到最终时间复杂度。

5.最坏情况

​	要保证即使在最坏情况，我们的程序还能满足要求的运行。

## 空间复杂度

1.Java中常见的内存占用

①基本数据类型

| 数据类型 | 内存占用字节数 |
| -------- | -------------- |
| byte     | 1              |
| short    | 2              |
| int      | 4              |
| long     | 8              |
| float    | 4              |
| double   | 8              |
| boolean  | 1              |
| char     | 2              |

②计算机访问内存的方式是一次读取一个字节

③一个变量要占用8个字节来表示

④创建一个对象，new Date()，会自动创建一个16字节的内存空间保存对象的<u>头信息</u>

⑤一般内存的使用，如果不足8个字节，会自动补全为8个字节：如数组，作为一个对象，需要16个字节保存自己的头信息，还需要4个字节保存数组长度，还需要多创建4个填充字节。

## 算法

### 一、简单排序

#### 1.Comparable接口

1. 用于对2个对象进行排序，比如Student、Product、Order......

2. 被排序的对象要实现Comparable接口，重写其中的compareto方法（给出特殊对象的比较规则）

   ```
   @Override
       public int compareTo(Student s) {
           return this.getAge()-s.getAge();
       }
   ```

   

3. 然后被比较对象（比如Student）就可以调用compareTo方法，实现比较

#### 2.冒泡排序

##### 原理

1.从左到右比较相邻的两个元素，如果左边比右边大，就交换这两个元素

2.每排一次，就冒出最大的一个泡泡

##### 时间复杂度

O(n^2)

##### 代码实现

```java
public class Bubble {

    public static void sort(Comparable[] a){
        for(int i=a.length-1;i>0;i--){
            for(int j=0;j<i;j++){
                if(greater(a[j],a[j+1])){
                    exchange(a,j,j+1);
                }
            }
        }
    }

    private static boolean greater(Comparable c1,Comparable c2){
        ;
        return c1.compareTo(c2)>0;
    }

    private static void exchange(Comparable[] a,int i,int j){
        Comparable temp;
        temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}
```

#### 3.选择排序

##### 原理

1.每次遍历，都假定待排序的第一个元素为最小值，把它的索引记为min，然后从这个元素开始，依次向右比较，如果有元素比这个元素更小，则把min更新为更小元素的索引，直到一次遍历结束，最后把索引为min的值与这次遍历待排序的第一个元素交换位置（即找到了最小值，并放到了待排序的元素的最左边位置）

2.撇开已经放好位置的元素，对剩余待排序的元素做1操作

##### 时间复杂度

O(n^2)

##### 代码实现

```java
public class Selection {

    public static void sort(Comparable[] a){
        for(int i=0;i<=a.length-2;i++){
            int minIndex = i;
            for(int j = i+1;j<a.length;j++){
                if(greater(a[minIndex],a[j])){
                    minIndex = j;
                }
            }
            exchange(a,minIndex,i);
        }
    }

    private static boolean greater(Comparable c1,Comparable c2){
        return c1.compareTo(c2)>0;
    }

    private static void exchange(Comparable[] a,int i,int j){
        Comparable temp;
        temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}
```

#### 4.插入排序

##### 原理

1.把所有元素分成2组，已排序的和未排序的

2.把未排序的第一个元素，插入到已排序的元素中

3.倒序遍历已排序的元素，如果未排序的第一个元素比取到的已排序的元素更小，则交换位置，继续比较，直到未排序的元素大于等于已排序的元素，就找到了这个未排序元素的正确插入位置

##### 时间复杂度

O(n^2)

##### 代码实现

```java
public class Insertion {

    public static void sort(Comparable[] a){
        for(int i=1;i<a.length;i++){

            for(int j=i;j>0;j--){
                if(greater(a[j],a[j-1])){
                    break;
                }else{
                    exchange(a,j,j-1);
                }
            }
        }
    }

    private static boolean greater(Comparable c1,Comparable c2){
        return c1.compareTo(c2)>0;
    }

    private static void exchange(Comparable[] a,int i,int j){
        Comparable temp;
        temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}
```

### 二、高级排序

#### 1.希尔排序

##### 原理

1.确定增长量h，对所有数据进行分组，对每一组数据进行插入排序

2.减小增长量h，对1中排序得到的中间结果进行插入排序排序，直到增长量为1时，排序得到的结果有序

```
# 初始增长量h的确定
int h = 1;
while(h<a.length/2){
	h = 2h+1;
}
# 增长量逐次减小的规则
h = h/2
```

##### 代码实现

```java
public class Shell {

    public static void sort(Comparable[] a){
        //1.确定增长量
        int h = 1;
        while(h<a.length/2){
            h = 2*h+1;
        }
        //2.进行分组插入排序
        while(h>=1){
            //2.1找到需要进行插入的元素
            for(int i=h;i<a.length;i++){
                //2.2把待插入的元素和同一组内前面的元素比较（每次循环，都和前h位置比较）
                for(int j=i;j>=h;j-=h){
                    if(greater(a[j],a[j-h])){
                        break;
                    }else{
                        exchange(a,j,j-h);
                    }
                }

            }
            h /= 2;
        }


    }

    private static boolean greater(Comparable c1,Comparable c2){
        return c1.compareTo(c2) > 0;
    }

    private static void exchange(Comparable[] a,int i,int j){
        Comparable temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}

```

#### 2.快速排序

##### 原理

1.先设定一个分界值，把比这个分界值小的元素放在它的左边，比分界值大的元素放在它的右边。

2.然后继续在（分界值的）左边部分（和右边部分）再分别设定一个分界值，重复1操作，直到全部有序

**切分数组的原理**

1.先找一个基准值，再用两个指针分别指向数组的头部（索引为0）和尾部（索引为a.length）

2.先让尾部指针向头部移动，寻找比基准值的小的元素，找到即停止，记录尾部指针的位置

3.再让头部指针向尾部移动，寻找比基准值大的元素，找到即停止，记录头部指针的位置

4.交换两个指针所指的元素

5.然后继续让尾部指针、头部指针移动，直到头部指针大于等于尾部指针（表明数组全部遍历一遍），最后交换两指针共同指向的元素和基准值，即完成一次排序

##### 代码实现

```java
public class Quick {


    public static void sort(Comparable[] a){
        int low = 0;
        int high = a.length-1;
        sort(a,low,high);
    }

    public static void sort(Comparable[] a,int low,int high){
        //1.安全性校验
        if(low>=high){
            return;
        }
        //2.分组
        int partition = partition(a, low, high);
        //这里的partition是排好序后的分界值所在的索引
        //3.让左子组有序
        sort(a,low,partition-1);
        //4.让右子组有序
        sort(a,partition+1,high);
    }

    public static int partition(Comparable[] a,int low,int high){
//        1.先找一个基准值，再用两个指针分别指向数组的头部（索引为0）和尾部（索引为a.length）
//        2.先让尾部指针向头部移动，寻找比基准值的小的元素，找到即停止，记录尾部指针的位置
//        3.再让头部指针向尾部移动，寻找比基准值大的元素，找到即停止，记录头部指针的位置
//        4.交换两个指针所指的元素
//        5.然后继续让尾部指针、头部指针移动，直到头部指针大于等于尾部指针（表明数组全部遍历一遍），最后交换两指针共同指向的元素和基准值，即完成一次排序

        int left = low;
        int right = high+1;
        while(true) {
            //1.让尾部指针向前扫描，如果它指向的元素比基准值小，则停止，记录此时right位置
            while (less(a[low], a[--right])) {
                if (right == low) {
                    break;
                }
            }
            //2.让头部指针向后扫描，如果它指向的元素比基准值大，则停止，记录此时left位置
            while (less(a[++left], a[low])) {
                if (left == high) {
                    break;
                }
            }
            //3.如果left<right，交换二者对应的元素；
            if (left < right) {
                exchange(a, left, right);
            }
            //4.如果left=right，则把left和right共同指向的元素和基准值交换位置；
            if (left >= right) {
                break;
            }
        }
        exchange(a,low,right);
        return right;
    }

    private static boolean less(Comparable v,Comparable w){
        return v.compareTo(w)<0;
    }

    private static void exchange(Comparable[] a,int i,int j){
        Comparable temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}
```

##### 时间复杂度

最优：O(nlogn)

最坏：O(n^2)

平均：O(nlogn)

### 三、排序稳定性

##### 定义

如果所有元素中有2个元素相等（如A=B），那么如果排序后的A、B两元素的顺序总是与初始时相同，则说明排序算法是稳定的，否则是不稳定的。

##### 意义

对于一次排序，排序的稳定性没意义；对于二次排序，则有意义（因为它保留了第一次排序的含义）

##### 排序稳定

冒泡排序、插入排序、归并排序

##### 排序不稳定

选择排序、希尔排序、快速排序

## 数据结构

### 线性表

#### 定义

一个具有n个相同特性的数据元素的有限序列

#### 术语

前驱元素

后继元素

#### 特征

1.第一个元素只有后继元素，为**头结点**

2.最后一个元素只有前驱元素，为**尾结点**

3.除了头尾结点，其余所有的元素都有且只有一个前驱元素和一个后继元素

#### 分类

分类依据——线性表中**数据存储的方式**不同（顺序存储、链式存储）

#### 顺序表

##### 基本实现

```java
public class SequenceList<T> {
    private T[] eles;
    private int N;

    public SequenceList(int capacity){
        this.eles = (T[]) new Object[capacity];
        this.N = 0;
    }

    public void clear(){
        N=0;
    }

    public boolean isEmpty(){

        return N==0;
    }

    public int length(){
        return N;
    }

    public T get(int i){
        return eles[i];
    }

    public void insert(int i,T t){
        N++；
        //1.把索引i及i之后的元素全部向右移一位
        for(int index=N-1;index>i;index--){
            eles[index] = eles[index-1];
        }
        //2.再把t赋值给索引为i的位置
        eles[i] = t;
    }

    public void insert(T t){
        eles[N++] = t;
    }

    public T remove(int i){
        //1.记录索引为i的值
        T current = eles[i];
        //2.用索引为i+1及之后的值依次覆盖前面相邻的位置
        for(int index=i;index<N-1;index++){
            eles[index] = eles[index+1];
        }
		N--;
        return current;
    }
    public int indexOf(T t){
        for(int i=0;i<N;i++){
            if(eles[i]==t){
                return i;
            }
        }
        return -1;
    }


}
```

##### 遍历

```java
//让SequenceList类实现Iterable接口
public class SequenceList<T> implements Iterable<T>{
//重写Iterator的Iterator方法
//该方法需要返回一个Iterator对象，而Iterator是抽象类，因此要先创建一个内部类（实现了Iterator的子类），用这个具体的内部类来创建Iterator对象
@Override
    public Iterator<T> iterator() {
        return new SIterator();
    }
	//创建一个内部类
    private class SIterator implements Iterator {
        //创建游标对象和构造方法
        private int cursor;
        public SIterator(){
            this.cursor = 0;
        }
	//重写next()、hasNext()方法
        @Override
        public boolean hasNext() {
            return cursor < N;
        }
	
        @Override
        public Object next() {
            return eles[cursor++];//注意是后++
        }
    }


}
```

##### 容量可变

**扩容、缩容原理**

1.扩容：当我们在插入元素时，检查数组长度是否等于数组容量，如果等于，则创建一个两倍大的新数组，把原数组中的数据全部拷贝到新数组，再添加元素

2.缩容：当我们在删除元素时，先删除元素，再检查数组长度是否小于数组容量的1/4，如果小于，则创建一个1/2倍的新数组，把原数组的数据拷贝到新数组

**代码实现**

```java
public class SequenceList<T> implements Iterable<T>{
    
    //重置数组eles的容量
    public void resize(int newSize){
        //1.创建一个临时变量指向原数组
        T[] temp = eles;
        //2.让eles这个变量指向newSize的新数组
        eles = (T[])new Object[newSize];
        //3.把原数组数据拷贝到新数组
        for(int i=0;i<N;i++){
            eles[i] = temp[i];
        }

    }
    //增加元素
     public void insert(T t){
        if(N== eles.length){
            resize(2*eles.length);
        }

        eles[N++] = t;
    }
    //删除元素
     public T remove(int i){
        //1.记录索引为i的值
        T current = eles[i];
        //2.用索引为i+1及之后的值依次覆盖前面相邻的位置
        for(int index=i;index<N-1;index++){
            eles[index] = eles[index+1];
        }
        N--;
        if(N<eles.length/4){
            resize(eles.length/2);
        }
        return current;
    }
    
    
}
```

##### 时间复杂度

get(int i):O(1)

insert(int i,T t):O(n)

remove(int i):O(n)

##### ArrayList实现

1.利用数组实现

2.有扩容操作

3.提供了遍历的方式

#### 链式表

##### 单向链表

###### 代码实现

```java
public class LinkedList<T>{

    private Node head;
    private int N;

    private class Node{
        T item;
        Node next;

        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }
    }

    public LinkedList(){
        this.head = new Node(null,null);
        this.N = 0;
    }

    public void clear(){
        head.next = null;
        this.N = 0;
    }

    public int length(){
        return N;
    }

    public boolean isEmpty(){
        return N==0;
    }

    public T get(int i){
        Node n = head.next;
        for(int index = 0;index < i;index++){
            n = n.next;
        }
        return n.item;
    }

    public void insert(T t){
        //1，找到当前最后一个结点
        Node n = head;
        while(n.next!=null){
            n = n.next;
        }
        //2.创建一个新结点，保存元素t
        Node newNode = new Node(t, null);
        //3.让最后一个结点指向新结点
        n.next = newNode;
        //4.元素个数+1
        N++;

    }

    public void insert(int i,T t){
        //1.找到第i-1个位置的结点
        Node pre = head;
        for(int index = 0;index<=i-1;index++){
            pre = pre.next;
        }
        //2.找到第i个位置的结点
        Node curr = pre.next;
        //3.创建一个新结点，保存数据t，同时让它指向原来第i个未知的结点
        Node newNode = new Node(t,curr);
        //4.让第i-1个位置的结点指向新结点
        pre.next = newNode;
        //5.元素的个数+1
        N++;
    }

    public T remove(int i){
        //1.找到i-1位置的结点
        Node pre = head;
        for(int index = 0;index<=i-1;index++){
            pre = pre.next;
        }
        //2.找到i位置的结点
        Node curr = pre.next;
        //3.找到i+1位置的结点
        Node nextNode = curr.next;
        //4.让i-1位置的结点指向i+1位置的结点
        pre.next = nextNode;
        //5.元素个数-1
        N--;
        return curr.item;
    }

    public int indexOf(T t){
        Node n = head;
        for(int i=0;n.next!=null;i++){
            n = n.next;
            if(n.item.equals(t)){
                return i;
            }
        }
        return -1;
    }

}
```

###### 提供遍历

```java
public class LinkedList<T> implements Iterable<T>{
    
    @Override
    public Iterator<T> iterator() {
        return new LIterator();
    }

    private class LIterator implements Iterator{
        private Node n;

        public LIterator(){
            this.n = head;
        }


        @Override
        public boolean hasNext() {
            return n.next!=null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }
    
}
```

##### 双向链表

###### 代码实现

```java
public class TwoWayLinkList<T> implements Iterable<T>{
    //头结点
    private Node head;
    //尾结点
    private Node last;
    //链表的长度
    private int N;



    //结点类
    private class Node{
        private T item;
        private Node pre;
        private Node next;


        public Node(T item,Node pre,Node next){
            this.item = item;
            this.pre = pre;
            this.next = next;
        }

    }

    public TwoWayLinkList(){
        //初始化头结点
        this.head = new Node(null,null,null);
        //初始化尾结点
        this.last = null;
        //初始化链表长度
        this.N = 0;
    }
    //清空链表
    public void clear(){
        head.next = null;
        last = null;
        N = 0;
    }
    //获取链表长度
    public int length(){
        return N;
    }
    //判断链表是否为空
    public boolean isEmpty(){
        return N==0;
    }
    //获取第一个元素
    public T getFirst(){
        if(isEmpty()){
            return null;
        }
        return head.next.item;
    }
    //获取最后一个元素
    public T getLast(){
        if(isEmpty()){
            return null;
        }
        return last.item;
    }

    //插入元素t
    public void insert(T t){

        if(isEmpty()){
            //如果链表为空
            //创建一个新结点，保存元素t
            Node newNode = new Node(t, head, null);
            //让新结点成为尾结点
            last = newNode;
            //让头结点指向尾结点
            head.next = last;
        }else{
            //如果链表不为空
            //创建一个新结点，保存元素t
            Node newNode = new Node(t, last, null);
            //让尾结点指向新结点
            last.next = newNode;
            //让新结点成为尾结点
            last = newNode;
        }
        N--;

    }
    //向指定位置插入一个元素t
    public void insert(int i,T t){
        //找到i-1位置的结点
        Node pre = head;
        for(int index=0;index<i;index++){
            pre = pre.next;
        }
        //找到i位置的结点
        Node curr = pre.next;
        //创建新结点
        Node newNode = new Node(t, pre, curr);
        //让i-1位置的结点指向新结点
        pre.next = newNode;
        //让新结点指向i位置的结点
        newNode.next = curr;

        //元素个数+1
        N++;
    }
    //获取i位置的元素
    public T get(int i){
        Node n = head;
        for(int index=0;index<=i;index++){
            n = n.next;
        }
        return n.item;
    }
    //找到元素t在链表中出现的第一个位置
    public int indexOf(T t){
        Node n = head;
        for(int i=0;n.next!=null;i++){
            n = n.next;
            if(n.item.equals(t)){
                return i;
            }
        }
        return -1;
    }
    //删除i位置的元素
    public T remove(int i){
        //找到i-1位置的结点
        Node pre = head;
        for(int index=0;index<i;index++){
            pre = pre.next;
        }
        //找到i位置的结点
        Node curr = pre.next;
        //找到i+1位置的结点
        Node nextNode = curr.next;
        //让i-1位置的结点指向i+1位置的结点
        pre.next = nextNode;
        //让i+1位置的结点指向i-1位置的结点
        nextNode.pre = pre;

        N--;
        return curr.item;
    }
    @Override
    public Iterator<T> iterator() {
        return new TWLLIterator();
    }

    private class TWLLIterator implements Iterator{
        private Node n;

        public TWLLIterator(){
            this.n = head;
        }

        @Override
        public boolean hasNext() {
            return n.next!=null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }
}

```

###### LinkedList

1.底层采用 双向链表 实现

2.结点由3个域构成

###### 时间复杂度

get(int i):O(n)

insert(int i,T t):O(n)	主要是因为需要从头到尾遍历，增加了时间复杂度

remove(int i):O(n)		主要是因为需要从头到尾遍历，增加了时间复杂度

相比于顺序表的**优势在于增删**，虽然增删的时间复杂度与顺序表相同，都是O(n)，但是因为链表主要是遍历耗费时间，不涉及到元素的位置交换，同时链表不需要提前指定空间，不涉及扩容等问题。

###### 选择

**查询多，顺序表；增删多，链表**

##### 单向链表

###### 反转

原理：

1.调用reverse(Node curr)方法反转原链表的第一个结点

2.如果发现它还有下一个结点，就递归调用reverse(Node curr)反转下一个结点

3.直到最后一个结点（没有了下一个结点），这时就到了递归出口，我们把head结点指向这最后一个结点

4.递归返回每一个结点反转后的结果

**代码实现**

```java
public class LinkedList<T> implements Iterable<T>{
public void reverse(){
        //如果链表为空，则结束运行；否则才反转
        if(isEmpty()){
            return;
        }
        reverse(head.next);
    }

    public Node reverse(Node curr){
        //递归出口：如果当前结点为尾结点，则让head结点指向当前结点
        if(curr.next==null){
            head.next = curr;
            return curr;
        }
        //如果当前结点还有下一个结点，则递归反转它的下一个结点
        Node pre = reverse(curr.next);
        //将“递归反转后的下一个结点”返回，让它指向当前结点（因为它就是链表反转后，当前结点的上一个结点）
        pre.next = curr;
        //让当前结点指向null
        curr.next = null;
        //返回反转后的当前结点
        return curr;
    }
}
```

###### 快慢指针

###### 中间值问题

**原理**

创建快慢两个指针，快指针每次走2步，慢指针每次走一步，这样当快指针走到链表末尾的时候，慢指针刚好走到链表中间，返回慢指针所指的结点，就是我们所找的中间值

**代码实现**

```java
public class FastSlowTest {
    public static void main(String[] args) {
        Node<String> seventh = new Node("gg",null);
        Node<String> sixth = new Node("ff",seventh);
        Node<String> fifth = new Node("ee",sixth);
        Node<String> fourth = new Node("dd",fifth);
        Node<String> third = new Node("cc",fourth);
        Node<String> second = new Node("bb",third);
        Node<String> first = new Node("aa",second);

        //查找中间值
        String mid = getMid(first);
        System.out.println("中间值是："+mid);

    }

    public static String getMid(Node<String> first){
        Node<String> fast = first;
        Node<String> slow = first;
        while(fast!=null && fast.next!=null){
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow.item;
    }

    //结点类
    private static class Node<T>{
        T item;
        Node next;

        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }
    }
}
```

###### 单向链表是否有环问题

**链表有环？无环？**

无环：链表中所有的结点都是从前指向后

有环：链表中存在后面的结点指向前面的结点的这种情况

**解决原理**（类似博尔特和你在直行道跑步还是在操场跑步）

**无环情况下：快慢指针是不可能相遇的，两者差距只会越差越大**

**有环情况下：快慢指针总会有相遇**

**代码实现**

```java
public class IsCircleCheckTest {
    public static void main(String[] args) {
        Node<String> seventh = new Node("gg",null);
        Node<String> sixth = new Node("ff",seventh);
        Node<String> fifth = new Node("ee",sixth);
        Node<String> fourth = new Node("dd",fifth);
        Node<String> third = new Node("cc",fourth);
        Node<String> second = new Node("bb",third);
        Node<String> first = new Node("aa",second);
        seventh.next = third;

        //是否有环
        boolean result = isCircle(first);
        System.out.println("是否有环："+result);

    }

    public static boolean isCircle(Node<String> first){
        //创建2个指针，遍历链表，如果链表有环，则遍历成为无限循环，循坏出口为“快慢指针相遇”，此时跳出循环，返回true
        //如果链表无环，则循环会在fast==null或者fast.next==null时跳出，此时返回false

        Node<String> fast = first;
        Node<String> slow = first;
        while(fast!=null&&fast.next!=null){
            fast = fast.next.next;
            slow = slow.next;
            if(fast.equals(slow)){
                return true;
            }
        }
        return false;
    }

    //结点类
    private static class Node<T>{
        T item;
        Node next;

        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }
    }
}
```

###### 有环链表的入口问题

**原理**

1.创建快慢指针，遍历链表

2.当快慢指针相遇，表示链表有环之后，创建一个临时指针temp指向链表中的头结点

3.让temp指针以和slow指针同样的步长1，对链表进行遍历，当temp与slow相遇时，此时所指的结点就是环的入口

**代码实现**

```java
public class CircleListInTest {
    public static void main(String[] args) {
        Node<String> seventh = new Node("gg",null);
        Node<String> sixth = new Node("ff",seventh);
        Node<String> fifth = new Node("ee",sixth);
        Node<String> fourth = new Node("dd",fifth);
        Node<String> third = new Node("cc",fourth);
        Node<String> second = new Node("bb",third);
        Node<String> first = new Node("aa",second);
        seventh.next = third;

        //是否有环
        Node res = getEntrance(first);
        System.out.println("环的入口："+res.item);

    }

    public static Node getEntrance(Node<String> first){
        Node<String> fast = first;
        Node<String> slow = first;
        Node<String> temp = null;

        while(fast!=null&&fast.next!=null){
            fast = fast.next.next;
            slow = slow.next;
            if(fast.equals(slow)){
                temp = first;
                continue;
            }

            if(temp!=null){
                temp = temp.next;
                if(temp.equals(slow)){
                    return temp;
                }
            }
        }
        return null;
    }

    //结点类
    private static class Node<T>{
        T item;
        Node next;

        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }
    }
}
```

##### 循环列表

让单向链表的尾结点指向头结点

##### 约瑟夫问题

###### 解决思路

1.先创建一个由41个元素构成的循环列表，每个元素记录的值分别是1，2，3，4......40，41

2.创建一个计数器count，模拟报数（将count初始值置为0，每有一个人报数，进行++，如果count=3，重新将count置为0）

3.每当count=3时，将该结点从链表中删除，并将此结点返回（我们想看自杀的顺序）

```java
public class JosephTest {
    public static void main(String[] args) {
        //解决约瑟夫问题

        //1.构建循环链表，包含41个结点，分别存储1-41之间的整数
        Node<Integer> first = null;
        Node<Integer> pre = null;
        for(int i=1;i<=41;i++){
            //如果是第一个结点
            if(i==1){
                first = new Node<Integer>(i,null);
                pre = first;
                continue;
            }
            else if(i>1 &&i<41){
                //如果是中间的结点
                Node<Integer> newNode = new Node<>(i, null);
                pre.next = newNode;
                pre = pre.next;
            }
            else{
                //如果是最后一个结点
                Node<Integer> lastNode = new Node<>(i, first);
                pre.next = lastNode;
            }
        }
        //2.需要count计数器，模拟报数
        int count=0;
        //3.遍历循环列表
        //记录当前结点，初始值是头结点first
        Node<Integer> curr = first;
        //记录当前结点的前一个结点（用于count=3时，删除当前结点）
        Node<Integer> before = null;
        //循环遍历
        while(curr!=curr.next){
            count++;
            if(count==3){
                //如果count=3，打印当前结点的值，删除当前结点，当前结点后移，count置为0
                System.out.print(curr.item+",");
                before.next = curr.next;
                curr = curr.next;
                count = 0;

            }else{
                //如果count！=3，before结点后移，当前结点后移
                before = curr;
                curr = curr.next;
            }
        }
        System.out.print(curr.item);
    }

    //结点类
    public static class Node<T>{
        private T item;
        private Node next;

        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }

    }
}
```

#### 栈

##### 定义

一种基于“**先进后出**”的数据结构，一种只能从**一端插入和删除**的特殊线性表

##### 术语

压栈：数据入栈的过程

弹栈：数据出栈的过程

##### 代码实现

```java
//用链表实现Stack
public class Stack<T> implements Iterable<T>{
    //成员变量：头结点，栈中的元素个数
    private Node head;
    private int N;



    //内部类：结点类
    private class Node{
        private T item;
        private Node next;

        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }

    }

    //构造方法
    public Stack(){
        this.head = new Node(null,null);
        this.N = 0;
    }

    public boolean isEmpty(){
        return N==0;
    }

    public int size(){
        return N;
    }

    public void push(T t){
        //为保证“先进后出”，所以后面插入的元素必须在前面插入的元素的前面
        //最后插入的元素就是头结点head指向的第一个结点

        //1.找到头结点指向的下一个结点
        Node nextNode = head.next;
        //2.创建新结点，保存数据t
        //3.让新结点指向头结点的下一个结点
        Node newNode = new Node(t, nextNode);
        //4.让头结点指向新结点
        head.next = newNode;
        //5.让元素个数+1
        N++;

    }
    public T pop(){
        //找到头结点指向的第一个结点
        Node firstNode = head.next;
        if(firstNode==null){
            return null;
        }
        //找到头结点之后的第二个结点
        Node secondNode = firstNode.next;
        //让头结点指向头结点之后的第二个结点
        head.next = secondNode;
        //元素个数-1
        N--;
        //返回原来“头结点指向的第一个结点”
        return firstNode.item;
    }

    @Override
    public Iterator<T> iterator() {
        return new SIterator();
    }

    private class SIterator implements Iterator{
        private Node n;
        public SIterator(){
            this.n = head;
        }
        @Override
        public boolean hasNext() {
            return n.next!=null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }

}
```

##### 括号匹配问题

###### 解决思路

1.创建一个栈来存储左括号

2.从左往右遍历字符串，获取每一个字符

3.判断字符是否是左括号，如果是，就把它存入栈中

4.判断字符是否是右括号，如果不是，就进入下一次遍历

5.如果是右括号，那就从栈中弹出一个元素t，如果弹出的元素为null，说明该右括号没有对应的左括号，括号不匹配；如果弹出的元素不为null，则继续遍历

6.遍历结束后，检查栈中是否还有剩余的左括号，如果有，说明括号不匹配

###### 代码实现

```java
public class BracketsMatchTest {
    public static void main(String[] args) {
        String str = "(上海(长安)())";
        boolean match = isMatch(str);
        System.out.println(str+"中的括号是否匹配："+match);
    }

    public static boolean isMatch(String str){
        //1.创建一个栈，用于存储左括号
        Stack<String> chars = new Stack<>();
        //2.从左到右遍历字符串，获取每一个字符
        for (int i = 0; i < str.length(); i++) {
            String s = str.charAt(i)+"";
            //3.判断该字符是不是左括号，如果是，则把它压入栈中
            if(s.equals("(")){
                chars.push("(");
            }else if(s.equals(")")){
                //4.如果不是，继续判断该字符是不是右括号，如果是，就从栈中弹出一个元素pop，
                // 判断弹出的pop元素是否为null，如果为null，返回false（说明括号不匹配）
                // 如果不为null，继续遍历
                String pop = chars.pop();
                if(pop==null){
                    return false;
                }

            }

        }

        //6.遍历结束后，检查栈中是否有剩余的左括号，如果有，返回false
        if(chars.size()!=0){
            return false;
        }

        return true;
    }
}

```

##### 逆波兰表达式问题

中缀表达式：二元运算符总是放在与它相关的两个操作数之间

逆波兰表达式（后缀表达式）：二元运算符总是放在与它相关的两个操作数后面

###### 解决思路

1.创建一个栈，用来保存操作数

2.从左到右遍历数组，获得每一个元素

3.判断该元素是否为运算符，如果不是运算符，就将它压入栈中，然后继续遍历

4.如果是运算符，就要从栈中弹出2个操作数o1,o2，对这2个操作数进行运算，并将运算结果压入栈中

5.当数组遍历结束后，最后在栈中剩余一个元素，它就是逆波兰表达式的值，将它弹出并返回即可

###### 代码实现

```java
public class ReversePolishNotationTest {
    public static void main(String[] args) {
        //中缀表达式3*(17-15)+18/6的波兰表达式如下
        String[] notation = {"3","17","15","-","*","18","6","/","+"};
        int result = caculate(notation);
        System.out.println("逆波兰表达式的结果是："+result);
    }

    public static int caculate(String[] notation){
        //1.创建一个栈，用于保存操作数
        Stack<Integer> oprands = new Stack<>();

        //2.从左往右遍历数组，得到每一个元素
        for (int i = 0; i < notation.length; i++) {
            String curr = notation[i];
            Integer o1;
            Integer o2;
            Integer result;
            switch(curr){
                case "+":
                    //4.如果是运算符，就从栈中弹出2个操作数，并进行运算，并将运算结果压入栈中
                    o1 = oprands.pop();
                    o2 = oprands.pop();
                    result = o2 + o1;
                    oprands.push(result);
                    break;
                case "-":
                    //4.如果是运算符，就从栈中弹出2个操作数，并进行运算，并将运算结果压入栈中
                    o1 = oprands.pop();
                    o2 = oprands.pop();
                    result = o2 - o1;
                    oprands.push(result);
                    break;
                case "*":
                    //4.如果是运算符，就从栈中弹出2个操作数，并进行运算，并将运算结果压入栈中
                    o1 = oprands.pop();
                    o2 = oprands.pop();
                    result = o2 * o1;
                    oprands.push(result);
                    break;
                case "/":
                    //4.如果是运算符，就从栈中弹出2个操作数，并进行运算，并将运算结果压入栈中
                    o1 = oprands.pop();
                    o2 = oprands.pop();
                    result = o2 / o1;
                    oprands.push(result);
                    break;
                default:
                    //3.判断该元素是不是运算符，如果不是，就把它压入栈中

                    oprands.push(Integer.parseInt(curr));
                    break;
            }

        }

        //5.遍历结束后，栈中剩余一个元素，即是逆波兰表达式的结果，将它弹出并返回
        Integer pop = oprands.pop();


        return pop;
    }
}
```

#### 队列

###### 定义

队列是一种基于**“先进先出”**的数据结构，是一种**“只能从一端进行插入，然后从另一端进行删除”**的特殊线性表

###### 特征

元素插入顺序和删除顺序是相同的

###### 代码实现

和栈一样，队列底层也是既可以用数组实现，也可以用链表实现，**我们这里用链表实现**

```java
public class Queue<T> implements Iterable<T>{
    //成员变量：头结点head、尾结点last、链表长度N

    //头结点head
    private Node head;
    //尾结点last
    private Node last;
    //链表长度N
    private int N;

    //定义内部类：结点类
    private class Node{
        private T item;
        private Node next;

        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }
    }

    //构造方法
    public Queue(){
        this.head = new Node(null,null);
        this.last = null;
        this.N = 0;
    }
    //判断链表是否为空
    public boolean isEmpty(){
        return N==0;
    }
    //返回队列中元素的个数（链表长度）
    public int size(){
        return N;
    }
    //向队列中插入元素——每插入一个元素，就让这个元素成为尾结点
    public void enqueue(T t){
        //找到旧的尾结点
        //如果旧的尾结点为空，则构造新结点赋给尾结点，让头结点指向新的尾结点
        if(last==null){
            last = new Node(t, null);
            head.next = last;
        }else{
            //如果旧的尾结点不为空，记录这个旧的尾结点
            Node oldLast = last;
            //创建新的结点，让它成为新的尾结点，让旧的尾结点指向新的尾结点
            last = new Node(t, null);
            oldLast.next = last;
        }
        //元素个数+1
        N++;
    }
    //从列表中拿出一个元素
    public T dequeue(){
        //如果是空队列，那么拿出的元素只能是null
        if(isEmpty()){
            return null;
        }
        //如果不是空队列，拿出元素的过程就是删除
        //记录将被删除的第一个元素为oldFirst,并将它的item返回
        Node oldFirst = head.next;
        //让头结点head指向oldFirst的下一个结点，实现oldFirst的删除
        head.next = oldFirst.next;
        //N--
        N--;

        //删除完成之后，检查队列是否为空，如果为空，则将last置为null
        if(isEmpty()){
            last = null;
        }
        return oldFirst.item;
    }

    @Override
    public Iterator<T> iterator() {
        return new QIterator() ;
    }

    private class QIterator implements Iterator{
        private Node n;

        public QIterator(){
            this.n = head;
        }

        @Override
        public boolean hasNext() {
            return n.next!=null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }
}
```

#### 符号表

##### 定义

用于存储一个键和一个值对应的**键值对**数据元素，我们可以根据键来查找值

##### 特点

在符号表中，**键具有唯一性**

日常应用

1. 字典          键-词语        值-释义
2. 书             键-目录         值-页码
3. 网页          键-关键词    值-网页

##### 代码实现

符号表既可以用数组实现，也可以用链表实现，**我们这里用链表实现**

```java
//用链表实现符号表
public class SymbolTable<Key,Value>{
    private Node head;
    private int N;

    private class Node{
        private Key key;
        private Value value;
        private Node next;

        public Node(Key key,Value value,Node next){
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }

    //构造方法
    public SymbolTable(){
        this.head = new Node(null,null,null);
        this.N = 0;
    }

    //获取符号表中键值对的个数
    public int size(){
        return N;
    }

    //往符号表中插入键值对
    public void put(Key key,Value value){
        //如果符号表中已经存在键为key的键值对，则修改该键值对的值为新传入的value
        Node n = head;
        while(n.next!=null){
            n = n.next;
            if(n.key.equals(key)){
                n.value = value;
                return;
            }
        }
        //如果符号表中不存在键为key的键值对，则创建一个新的结点，保存传入的key和value，将新的结点插入到头结点之后的第一个结点
        // 元素个数+1
        Node newNode = new Node(key, value, null);
        Node oldFirst = head.next;
        newNode.next = oldFirst;
        head.next = newNode;
        N++;

    }

    //删除符号表中键为key的键值对
    public void delete(Key key){
        //遍历，找到键为key的结点的上一个结点
        Node n = head;
        while(n.next!=null){
            if(n.next.key.equals(key)){
                //让“键为key的上一个结点”指向“键为key的下一个结点”，实现删除，元素个数-1
                n.next = n.next.next;
                N--;
                return;
            }
            n = n.next;
        }
    }

    //从符号表中获取key对应的值
    public Value get(Key key){
        //找到键为key的结点，返回该结点的值
        Node n = head;
        while(n.next!=null){
            n = n.next;
            if(n.key.equals(key)){
                return n.value;
            }
        }
        return null;
    }

}
```

##### 有序符号表

###### 定义

按照键值由小到大排序的符号表

###### 代码实现

**只需要修改符号表的代码实现中的put方法即可，其余方法都一样**

```java
public void put(Key key,Value value){
    //因为有序符号表是依据key进行排序，所以泛型Key要提供比较规则——即泛型Key要继承于Comparable类

    //遍历符号表，得到每一个结点，如果该结点对应的key大于传入的key，则结束遍历
    // 同时记录该结点和该结点对应的前一个结点

    Node curr = head.next;
    Node pre = head;
    while(curr!=null){

        //如果该结点对应的key等于传入的key，则修改该节点的value
        if(curr.key.compareTo(key)==0){
            curr.value = value;
            return;
        }

        if(curr.key.compareTo(key)>0){
            break;
        }
        pre = curr;
        curr = curr.next;
    }
    if(curr!=null){
        //创建新结点，保存传入的key和value，让新结点指向当前结点，让“当前结点的上一个结点”指向新结点
        Node newNode = new Node(key, value, curr);
        pre.next = newNode;
        //元素个数+1
        N++;
        //return;
        return;
    }else{
        //如果插入的key为最大值，将它插入最后
        Node lastNode = new Node(key, value, null);
        pre.next = lastNode;
        N++;
        return;
    }


}
```

### 二叉树

#### 树

定义

树是由**n（n>=1）个有限结点**组成的具有**层次关系**的集合

##### 特点

1. 每个结点都有零个或多个子结点
2. 没有父节点的结点是根结点
3. 每一个非根结点都有且只有一个父结点
4. 每个结点连同它的后代结点可以看成一棵树，称为当前结点的父节点的一棵子树

##### 术语

**结点的度：**一个结点所含有的子数个数就是该结点的度。

**叶结点：**度为0的结点就是叶结点。

**分支结点：**度不为0的结点就是分支结点。

**结点的层次：**<u>从根结点开始，根结点的层次为1，根的直接后继结点的层次为2，以此类推</u>

**结点的层序编号：**将树中的结点，按照从上到下，从左到右的次序排成一个线性序列，把他们变成一个连续的自然数。

**树的度：**树中所有结点度的最大值。

**树的高度(深度)：**<u>树中结点的最大层次。</u>

**森林：**将一棵非空树中的根结点删去，树就变成一个森林；给森林增加一个统一的根结点，森林就变成一棵树

**孩子结点：**一个结点的直接后继结点称为该结点的孩子结点。

**父结点：**一个结点的直接前驱结点称为该结点的父结点。

**兄弟结点：**具有同一个父结点的孩子结点称为兄弟结点。

##### 二叉树

###### 定义

度不超过2的树就叫二叉树

###### 满二叉树

每一层的结点数都达到最大值（2^(k-1)，k为该层的层数）的二叉树

###### 完全二叉树

叶子结点只出现在最下层和次下层的二叉树

##### 二叉查找树

###### 代码实现

**实现：**既可以用数组实现，也可以用链表实现。**我们现在先用链表实现，学到栈再用数组实现。**

**插入方法put实现原理**

1. 如果树为空，则让新结点成为根结点
2. 如果已经有了根结点，则从根结点开始遍历
   1. 如果新结点的key大于当前结点的key，则继续找当前结点的右子树
   2. 如果新结点的key小于当前结点的key，则继续找当前结点的左子树
   3. 如果新结点的key等于当前结点的key，则修改当前结点的value

```java
public void put(Key key,Value value){
        root = put(root,key,value);
    }
//在子树x上插入一个新的结点，并将新的子树返回
public Node put(Node x,Key key,Value value){
    //如果子树x为空，则创建一个新结点返回（元素个数+1）
    if(x==null){
        N++;
        return new Node(key,value,null,null);
    }else{
        //如果子树x不为空
        //比较传入的key和子树x的键：
        int cmp = key.compareTo(x.key);
        if(cmp<0){
            //如果传入的key小于子树x的键，则将传入的key,value插入子树x的左子树
            x.left = put(x.left, key, value);
        }else if(cmp>0){
            //如果传入的key大于子树x的键，则将传入的key，value插入子树x的右子树
            x.right = put(x.right,key,value);
        }else{
            //如果传入的key等于子树x的键，则将结点x的值修改为value
            x.value = value;
        }
    }

    return x;

}
```

**查找方法get实现原理**

1. 如果子树x为空，则返回null
2. 如果子树x不为空，从根结点开始：
   1. 如果待查找的key小于当前结点的key，则递归在当前结点的左子树查找；
   2. 如果待查找的key大于当前结点的key，则递归在当前结点的右子树查找；
   3. 如果待查找的key等于当前结点的key，则返回当前结点的值

```java
public Value get(Key key){
    return get(root,key);
}

public Value get(Node x,Key key){
    //如果子树x为空，则直接返回null
    if(x==null){
        return null;
    }
    //如果子树不为空，比较传入的key和子树x的键：
    int cmp = key.compareTo(x.key);
    if(cmp<0){
        //如果传入的key小于子树x的键，则继续去x的左子树查找
        return get(x.left, key);
    }else if(cmp>0){
        //如果传入的key大于子树x的键，则继续去x的右子树查找
        return get(x.right,key);
    }else{
        //如果传入的key等于子树x的键，则返回子树x的值
        return x.value;
    }
}
```

**删除方法delete实现原理**

1. 如果树为空，则return null；
2. 如果树不为空，则：
   1. 先找到待删除结点的右子树中的键最小的结点
   2. 让“待删除结点的左子树”成为“待删除结点的右子树中的键最小的结点”的左子树
   3. 让“待删除结点的右子树”成为“待删除结点的右子树中的键最小的结点”的右子树
   4. 让“待删除结点的上一个结点”指向“待删除结点的右子树中的键最小的结点”

**代码实现**

```java
//在整棵树上删除键为key的结点
public void delete(Key key){
    delete(root,key);
}
//删除子树x上键为key的结点，并将新的字数返回
public Node delete(Node x,Key key){
    //如果子树x为空，直接return null
    if(x==null){
        return null;
    }
    //如果子树x不为空
    //先找到x结点的子树中键为key的结点——比较key和x结点的键：
    int cmp = key.compareTo(x.key);
    if(cmp<0){
        //如果key小于x结点的键，则继续往结点x的左子树找
        x.left = delete(x.left, key);
    }else if(cmp>0){
        //如果key大于x结点的键，则继续往结点x的右子树找
        x.right = delete(x.right,key);
    }else{
        //如果key等于x结点的键，则结点x就是我们要删除的元素，在这里真正实现删除操作
        if(x.right==null){
            return x.left;
        }
        if(x.left==null){
            return x.right;
        }
        //1.先找到结点x的右子树中的键最小的结点
        Node minNode = x.right;
        while(minNode.left!=null){
            minNode = minNode.left;
        }
        //2.断开这个“右子树中键最小的结点”和它的上一个结点
        Node n = x.right;
        while(n.left.left !=null){
            n = n.left;
        }
        n.left = null;

        //3.让x结点的左子树成为这个“右子树中键最小的结点”的左子树
        minNode.left = x.left;
        //4.让x结点的右子树成为这个“右子树中键最小的结点”的右子树
        minNode.right = x.right;
        //5.让x结点的父结点指向这个“右子树中键最小的结点”
        x = minNode;
        //6.元素个数+1
        N++;
    }
}
```

**查找二叉树中最小的键**

```java
//查找整个树中最小的键
public Key min(){
    return min(root).key;
}
//查找结点x的子树中键最小的结点
public Node min(Node x){
    //如果结点x还有左子树，就继续向左找；如果结点x没有左子树，说明结点x就是键最小的结点，直接把x返回
    if(x.left!=null){
        return min(x.left);
    }else{
        return x;
    }
    
}
```

**查找二叉树中最大的键**

```java
//查找二叉树中最大的键
public Key max(){
    return max(root).key;
}
//查找结点x的子树中最大的结点
public Node max(Node x){
    //如果x还有右子树，就继续往右走，如果x没有右子树了，说明x就是键最大的结点，直接把x返回
    if(x.right!=null){
        return max(x.right);
    }else{
        return x;
    }
}
```

###### 基础遍历

按照根结点什么时候被访问，分为：

1. 前序遍历：根结点，左子树，右子树
2. 中序遍历：左子树、根结点、右子树
3. 后序遍历：左子树、右子树、根结点

```java
//前序遍历
    //获取整个树中所有的键
    public Queue<Key> preErgodic(){
        Queue<Key> keys = new Queue<>();
        preErgodic(root,keys);
        return keys;
    }

    //获取指定树x的所有键，并放到keys队列中
    public void preErgodic(Node x,Queue<Key> keys){
        if(x==null){
            return;
        }
        //先把x结点的key放入keys中
        keys.enqueue(x.key);
        //再递归遍历x结点的左子树
        preErgodic(x.left,keys);
        //再递归遍历x结点的右子树
        preErgodic(x.right,keys);
    }
```

```java
//中序遍历
//获取整个树中所有的键，并返回键的集合
public Queue<Key> midErgodic(){
    Queue<Key> keys = new Queue<>();
    midErgodic(root,keys);
    return keys;
}
//获取指定树的所有的键，并存放到keys中
public void midErgodic(Node x,Queue<Key> keys){
    if(x==null){
        return;
    }
    //先递归，遍历左子树，把左子树中的键全部存放到keys中
    midErgodic(x.left,keys);
    //再把根结点的键存放到keys中
    keys.enqueue(x.key);
    //再递归，遍历右子树，把右子树中的键全部存放到keys中
    midErgodic(x.right,keys);

}
```

```java
//后序遍历
//获取整个树的所有键，并把存放键的集合返回
public Queue<Key> afterErgodic(){
    Queue<Key> keys = new Queue<>();
    afterErgodic(root,keys);
    return keys;
}
//获取指定树的所有键，并把它们存放到Keys中
public void afterErgodic(Node x,Queue<Key> keys){
    if(x==null){
        return;
    }
    //先递归遍历左子树，把左子树中的所有键都放到keys中
    afterErgodic(x.left,keys);
    //再递归遍历右子树，把右子树中所有的键都放到keys中
    afterErgodic(x.right,keys);
    //再把根结点x的键放到keys中
    keys.enqueue(x.key);
}
```

###### 高级遍历

**层序遍历：**从根结点（第一层）开始，依次向下，获取每一层所有结点的值（每一层按照从左往右的顺序遍历）

**解决思路：**

1. 先创建一个队列，保存每一层的结点
2. 利用循环，从队列中弹出结点（循环结束条件：队列中没有结点可以弹出）
   1. 先把弹出的这个结点的键存入keys中
   2. 判断这个结点有没有左子结点，如果有，把它的左子节点存入队列
   3. 判断这个结点有没有右子节点，如果有，把它的右子节点存入队列中

**代码实现：**

```java
//层序遍历
public Queue<Key> layerErgodic(){
    //首先创建两个队列，分别用来保存键和结点
    Queue<Key> keys = new Queue<>();
    Queue<Node> nodes = new Queue<Node>();
    //保存结点的队列中，默认存入根结点root
    nodes.enqueue(root);
    //从保存结点的队列中，弹出一个结点，并把这个结点的key值存入keys中
    while(!nodes.isEmpty()){
        Node n = nodes.dequeue();
        keys.enqueue(n.key);
        //判断弹出的这个结点有没有左子节点，如果有，把它的左子节点存入队列nodes中
        if(n.left!=null){
            nodes.enqueue(n.left);
        }
        //判断弹出的这个结点有没有右子节点，如果没有，把它的右子节点存入队列nodes中
        if(n.right!=null){
            nodes.enqueue(n.right);
        }

    }

    return keys;
}
```

###### 最大深度问题

```java
//获取整个树的最大深度
public int maxDepth(){
    return maxDepth(root);
}
//获取指定树x的最大深度
public int maxDepth(Node x){
    if(x==null){
        return 0;
    }
    //定义3个变量，保存左子树的最大深度、右子树的最大深度、整个树的最大深度
    int max = 0;
    int maxL = 0;
    int maxR = 0;
    //先递归，获取x的左子树的最大深度
    maxL = maxDepth(x.left);
    //再递归，获取x的右子树的最大深度
    maxR = maxDepth(x.right);
    //比较左子树的最大深度和右子树的最大深入，取较大的那个+1，即为整个树的最大深度
    max = maxL>maxR?maxL+1:maxR+1;
    return max;
}
```

#### 堆

##### 特性

1. 堆是完全二叉树，即树的最后一个元素之前的所有的位置都是满的

2. 堆通常用数组实现，索引为k的结点的父结点的索引为k/2，索引为k的结点的子结点的索引为2k和2k+1；

   索引为k的元素，通过k/2就可来到上一层；索引为k的元素，通过2k就可来到下一层

3. 堆的父结点大于等于它的两个子结点，但是这两个子结点的顺序没有特殊要求

##### 堆中构造方法的实现

```java
public Heap(int capacity){
    //因为T继承至Comparable类，所以不能用Object数组强制转换，而是要用一个实现了Comparable的数组
    //如果用Object[]，会爆错ClassCastException
    //同时，因为我们将数组的0索引处废弃，为保证真实容量和用户想要的容量一致，所以我们的capacity+1
    this.items = (T[]) new Comparable[capacity+1];
    this.N = 0;
}
```

##### 堆中插入算法的实现

**原理：** 通过不断比较索引为k的元素的值和其父结点的值，如果父结点的值比索引为k处的结点的值小，则交换两个结点（循环结束的条件，k=1）

```java
//往堆中插入一个元素
public void insert(T t){
    items[++N] = t;
    swim(N);
}
//使用上浮算法，使索引k处的元素能在堆中有一个正确的位置
public void swim(int k){
    //通过不断比较索引为k的元素的值和其父结点的值，如果父结点的值比索引为k处的结点的值小，则交换两个结点
    while(k>1){
        if(less(k/2,k)){
            exchange(k/2,k);
        }
        k = k/2;
    }
}
```

##### 堆中删除算法的实现

###### 原理

**要删除堆中的最大值，即删除堆的根结点**

1. 先把堆中最后一个元素和根结点交换，将交换后的根结点所在位置的元素置为null
2. 堆新的根结点采用下沉算法，让新的根结点不断与它的较大子结点进行比较，如果根结点的值小于它的较大子结点，就交换两者的位置，这样不断向下移动，直到它没有子结点，或者它比自己子结点中的较大的那一个结点更大

```java
//删除堆中最大的元素（即索引为1的元素），并返回这个元素
public T delMax(){
    //记录堆中最大的元素，即堆的根结点
    T max = items[1];
    //交换根结点（索引为1）和堆中最后一个元素（索引为N），让最后一个元素成为临时根结点
    exchange(1,N);
    //删除掉交换后的根结点（索引为N）
    items[N] = null;
    //元素个数-1
    N--;
    //使用下沉算法，调整堆，让临时根结点进行下沉，找到正确的位置
    sink(1);
    //返回堆中最大的元素
    return max;
}

//使用下沉算法，使索引k处的元素能在堆中有一个正确的位置
public void sink(int k){
    //通过循环，不断地比较当前结点k和它的左子节点2*k以及右子结点2*k+1处的较大值
    //循环结束条件：1.当前结点没有子结点（2*k>N）2.当前结点比它的子结点中的较大值还要大
    while(2*k<=N){
        //获取当前结点k的左子节点2*k以及右子结点2*k+1处的较大值的索引
        int max;
        if(2*k+1<=N){
            if(less(2*k,2*k+1)){
                max = 2*k+1;
            }else{
                max = 2*k;
            }
        }else{
            max = 2*k;
        }
        //如果当前结点小，则交换当前结点和较大值的位置
        if(less(k,max)){
            exchange(k,max);
            //变换当前结点的位置，k = 子结点中较大值的索引，继续比较
            k = max;
        }else{
            break;
        }
    }
}
```

##### 堆排序

构造堆createHeap

```java
//根据原数组，构造出堆heap
private static void createHeap(Comparable[] source,Comparable[] heap){
    //1.先把soure数组中的元素拷贝到heap中，heap中的元素构成一个无序的堆
    System.arraycopy(source,0,heap,1,source.length);
    //2.对堆中的元素（长度的一半位置处，向索引为1的位置扫描）进行下沉操作，让堆有序
    //因为只有分支节点才需要下沉操作（最后一个分支节点的索引为：数组长度/2）
    for(int i = heap.length/2;i > 0;i--){
        sink(heap,i,heap.length-1);
    }
}
```

堆下沉

```java
//在heap堆中，对target处的元素坐下沉，范围是0-range
private static void sink(Comparable[] heap,int target,int range){
    //通过循环，找到当前结点的两个子结点中的较大值
    while(2*target<=range){ //只要它是个分支结点，我们就要检查它的左右结点的较大值
        int max;
        if(2*target+1<=range){
            if(less(heap,2*target,2*target+1)){
                max = 2*target+1;
            }else{
                max = 2*target;
            }
        }else{
            max = 2*target;
        }
        //把当前结点和这个较大值比较，如果当前结点小，则交换当前结点和较大值的位置；否则，退出循环
        if(less(heap,target,max)){
            exchange(heap,target,max);
            target = max;
        }else{
            break;
        }

    }

}
```

###### 堆排序

```java
public class HeapSort {
//判断heap堆中索引为i处的元素是否小于索引为j处的元素
private static boolean less(Comparable[] heap,int i,int j){
    return heap[i].compareTo(heap[j]) < 0;
}
//交换heap堆中i索引和j索引处的值
private static void exchange(Comparable[] heap,int i,int j){
    Comparable temp = heap[i];
    heap[i] = heap[j];
    heap[j] = temp;
}
//根据原数组，构造出堆heap
private static void createHeap(Comparable[] source,Comparable[] heap){
    //1.先把soure数组中的元素拷贝到heap中，heap中的元素构成一个无序的堆
    System.arraycopy(source,0,heap,1,source.length);
    //2.对堆中的元素（长度的一半位置处，向索引为1的位置扫描）进行下沉操作，让堆有序
    //因为只有分支节点才需要下沉操作（最后一个分支节点的索引为：数组长度/2）
    for(int i = (heap.length-1)/2;i > 0;i--){
        sink(heap,i,heap.length-1);
    }
}
//对source数组中的数据从小到大排序
public static void sort(Comparable[] source){
    //1.构建堆，调用createHeap方法，构建一个有序堆
    Comparable[] heap = new Comparable[source.length+1];
    createHeap(source,heap);
    //2.通过循环，不断交换索引为1位置的元素和“未排序的元素中索引最大的元素”
    //创建变量，记录“未排序的元素中索引最大的元素”
    int tempMax = heap.length-1;
    while(tempMax!=1){
        exchange(heap,1,tempMax);
        //3.交换完毕后，排除掉“已排序的元素——即索引最大位置的元素”，对剩余元素进行下沉操作
        tempMax--;
        sink(heap,1,tempMax);
    }

    //4.循环结束后，将排好序的堆heap中的元素复制到source中，即排好序
    System.arraycopy(heap,1,source,0,source.length);

}
//在heap堆中，对target处的元素坐下沉，范围是0-range
private static void sink(Comparable[] heap,int target,int range){
    //通过循环，找到当前结点的两个子结点中的较大值
    while(2*target<=range){ //只要它是个分支结点，我们就要检查它的左右结点的较大值
        int max;
        if(2*target+1<=range){
            if(less(heap,2*target,2*target+1)){
                max = 2*target+1;
            }else{
                max = 2*target;
            }
        }else{
            max = 2*target;
        }
        //把当前结点和这个较大值比较，如果当前结点小，则交换当前结点和较大值的位置；否则，退出循环
        if(less(heap,target,max)){
            exchange(heap,target,max);
            target = max;
        }else{
            break;
        }

    }

}
}
```

##### 优先队列

**基于堆这种数据结构实现**

###### 最大优先队列

特点：可以获取并删除队列中的最大值

基于**最大堆**实现：

1. 最大的元素在索引1处
2. 每个结点都比它的两个子结点要大

**代码实现**

```java
public class MaxPriorityQueue<T extends Comparable<T>> {
    //成员变量：用于存放元素的数组items、用于记录堆中元素个数的N
    //用于存放元素的数组items
    private T[] items;
    //用于记录堆中元素个数的N
    private int N;

    //构造方法
    public MaxPriorityQueue(int capacity){
        this.items = (T[])new Comparable[capacity+1];
        this.N = N;
    }

    //成员方法
    //获取队列中的元素个数
    public int size(){
        return N;
    }

    //判断队列是否为空
    public boolean isEmpty(){
        return N==0;
    }

    //判断队列索引为i处的元素是否小于索引为j处的元素
    public boolean less(int i,int j){
        return items[i].compareTo(items[j])<0;
    }

    //交换堆中i索引和j索引处的值
    public void exchange(int i,int j){
        T temp = items[i];
        items[i] = items[j];
        items[j] = temp;
    }

    //往堆中插入一个元素
    public void insert(T t){
        items[++N] = t;
        swim(N);
    }

    //删除堆中最大的元素，并返回这个元素
    public T delMax(){
        T max = items[1];
        exchange(1,N);
        N--;
        sink(1);
        return max;
    }

    //对插入的元素使用上浮算法，让索引为k处的元素在堆中处于一个正确的位置
    public void swim(int k){
        //比较当前结点和它的父结点的大小
        //通过循环进行比较，如果当前结点比父结点大，则交换它俩的位置，同时变换当前结点的位置
        //循环结束的条件：当前结点为根结点时（即k=1时）
        while(k!=1){
            if(less(k/2,k)){
                exchange(k/2,k);
                k = k/2;
            }
        }

    }

    //对要删除的元素使用下沉算法，让索引为k处的元素在堆中处于一个正确的位置
    public void sink(int k){
        //通过循环，不断比较当前结点和它的两个子结点的较大值的大小关系
        //如果当前结点比那个较大值小，则交换它俩位置，同时变换当前结点的位置
        //如果当前结点大于等于那个较大值，则下沉结束，退出循环
        while(2*k<=N){
            //当前结点有子结点时，首先找出子结点中的较大值对应的索引
            int max;
            if(2*k+1<=N){
                if(less(2*k,2*k+1)){
                    max = 2*k+1;
                }else{
                    max = 2*k;
                }
            }else{
                max = 2*k;
            }
            //比较当前结点和它的两个子结点的较大值的大小关系
            if(less(k,max)){
                exchange(k,max);
                k = max;
            }else{
                break;
            }


        }
    }

}
```

###### 最小优先队列

特点：可以获取并删除队列中的最小值

基于**最小堆**实现：

1. 最小的元素在索引1处
2. 每个结点都比它的两个子结点要小

**代码实现**

```java
package priority;

public class MinPriorityQueue<T extends Comparable<T>> {
    private T[] items;
    private int N;

    public MinPriorityQueue(int capacity){
        this.items = (T[]) new Comparable[capacity+1];
        this.N = 0;
    }

    public int size(){
        return N;
    }

    public boolean isEmpty(){
        return N==0;
    }

    private boolean less(int i,int j){
        return items[i].compareTo(items[j])<0 ;
    }

    private void exchange(int i,int j){
        T temp = items[i];
        items[i] = items[j];
        items[j] = temp;
    }

    public void insert(T t){
        items[++N] = t;
        swim(N);
    }

    public T delMin(){
        T min = items[1];
        exchange(1,N);
        N--;
        sink(1);
        return min;
    }

    private void swim(int k){
        //通过循环。不断比较当前结点和它的父结点，如果当前结点更小，则交换它俩位置，同时变换当前结点
        while(k!=1){
            if(less(k,k/2)){
                exchange(k,k/2);
                k = k/2;
            }
            else{
                //如果当前结点更大，则上浮结束
                break;
            }
        }
        //循环结束的条件：当前结点是根结点（即k=1）
    }

    private void sink(int k){
        //通过循环，不断比较当前结点和“它的两个子结点中较小的那个”
        while(2*k<=N){
            int min;
            if(2*k+1<=N){
                if(less(2*k,2*k+1)){
                    min = 2*k;
                }else{
                    min = 2*k+1;
                }
            }else{
                min = 2*k;
            }

            if(less(k,min)){
                break;
            }

            exchange(k,min);
            k = min;
        }
        //如果当前结点比较小值还要小，则下沉结束
        //如果当前结点比较小值还要大，则交换当前结点和较小值，变换当前结点
        //循环结束条件：没有子结点
    }

}
```

###### 索引优先队列

**实现思路：**

1. 创建三个数组，T[] items、int[] pq、int[] qp
2. T[] items          用于保存数据元素，构建数据元素与索引之间的对应关系——得到无序堆
3. int[] pq             用于保存items中数据元素对应的索引，对pq进行堆调整——得到有序堆
4. int[] qp             对pq进行逆序，用于保存pq的索引——当需要对items中的数据元素进行修改时，可以先通过qp找到“pq中被修改元素对应的索引”，再通过pq找到索引对应的值（即与items中的数据元素相对应的索引），从而实现修改

**代码实现**（部分）

[参见视频](https://www.bilibili.com/video/BV1iJ411E7xW?p=116)

```java
package priority;

public class IndexMinPriorityQueue<T extends Comparable<T>>{
    //存储堆中的元素，并关联索引
    private T[] items;
    //存储每个元素在items中的索引，pq数组需要堆有序
    private int[] pq;
    //保存pq的逆序，pq的值做qp的索引，pq的索引做qp的值
    private int[] qp;
    //记录堆中元素的个数
    private int N;

    public IndexMinPriorityQueue(int capacity){
        this.items = (T[]) new Comparable[capacity+1];
        this.pq = new int[capacity+1];
        this.qp = new int[capacity];
        this.N = 0;

        //默认情况下，items中没有存元素，所以我们让qp中的所有元素都为-1
        for (int i = 0; i < qp.length; i++) {
            qp[i] = -1;
        }
    }
    public int size(){
        return N;
    }
    public boolean isEmpty(){
        return N==0;
    }
    private boolean less(int i,int j){
        return items[pq[i]].compareTo(items[pq[j]])<0;
    }
    private void exchange(int i,int j){
        //交换pq中索引为i、j处的值
        int temp = pq[i];
        pq[i] = pq[j];
        pq[j] = temp;
        //修改qp中索引为pq[i]处的值为i，pq[j]处的值为j
        qp[pq[i]] = i;
        qp[pq[j]] = j;
    }
    //判断索引k对应的元素是否存在
    public boolean contains(int k){
        return qp[k] != -1;
    }
    //得到最小元素关联的索引
    public int minIndex(){
        return pq[1];
    }
    //往队列中插入一个元素，并关联索引i
    public void insert(int i,T t){
        //判断i是否被关联，如果已经被关联，则结束运行
        if(contains(i)){
            return;
        }
        //先把t插入到items的i位置
        items[i] = t;
        //元素个数+1
        N++;
        //让pq中记录这个索引i
        pq[N] = i;
        //让qp中索引为i的位置记录pq中的索引
        pq[i] = N;
        //让pq新插入的元素上浮，找到正确的位置
        swim(N);
    }
    //删除队列中最小的元素，并返回该元素关联的索引
    public int delMin(){
        return -1;
    }
    //删除索引i关联的元素
    public void delete(int i){

    }
    //把与索引i关联的元素修改为t
    public void changeItem(int i){

    }
    //使用上浮算法，使索引k处的元素能在堆中处于一个正确的位置
    public void swim(int k){

    }
    //使用下沉算法，使索引k所在的元素能在堆中处于一个正确的位置
    public void sink(int k){

    }
}
```

### 平衡树

#### 2-3查找树

##### 优势

保证树的平衡性，降低树的深度，提高查询效率

##### 定义

由2-结点和3-结点构成：

- 2-结点：一个键和两条链，左链接上所有的结点都小于该结点，右链接上所有的结点都大于该结点
- 3-结点：两个键和三条链，左链接上所有的结点都小于两个键中较小的，中链接上所有的结点都介于两个键之间，右链接上所有的键都大于两个键中较大的

##### 定义

1. 任意空链接到根结点的路径长度都相等
2. 临时4-结点变换为3-结点时，树的高度不会变化，只有当根结点是临时4-结点，分解根结点时，树高+1
3. 2-3树与普通二叉查找树最大的区别在于：**普通二叉树是自顶向下生长，而2-3树是自底向上生长**

#### 红黑树

##### 定义

红黑树是**含有红黑链接的满足以下条件的二叉查找树**

1. 红链接都是左链接
2. 没有一个结点可以同时连着两条红链接
3. 所有的空链接到根结点的黑链接路径都是相等的

##### 结点

##### 平衡化

###### 左旋

当某个结点的**左子节点为黑色，右子结点为红色**，此时需要左旋

前提：当前结点为h，它的右子结点为x

左旋过程：

1. 让x的左子节点成为h的右子结点      h.right = x.left;
2. 让h成为x的左子节点                         x.left = h;
3. 把h的color属性赋给x的color属性   x.color = h.color;
4. 让h.color置为true                             h.color = true;

###### 右旋

当某个结点的**左子结点是红色，左子结点的左子结点也是红色**，需要右旋

前提：当前结点为h，它的左子结点为x

右旋过程：

1. 让x的右子结点变成h的左子结点    h.left = x.right;
2. 让h变成x的右子结点                        x.right = h;
3. 将h的color属性赋给x的color属性  x.color = h.color;
4. 将h的color属性赋为true                 h.color = true;

###### 插入

**插入的结点都是红结点**

**根结点的颜色总是黑色**

1. 向单个的2-结点中插入新键
   - 如果新键比当前结点小，就让它成为当前结点的左链接，同时让它成为一个红结点
   - 如果新键比当前结点大，就让它成为当前结点的右链接，同时让它成为一个红结点，然后左旋，让红链接变成左链接
2. 向底部的2-结点插入新键
   - 如果新键比当前结点小，就让它成为当前结点的左链接，同时让它成为一个红结点
   - 如果新键比当前结点大，就让它成为当前结点的右链接，同时让它成为一个红结点，然后左旋，让红链接变成左链接

3.颜色反转

​		当当前结点的左子结点和右子结点都为红色时（临时的4-结点），我们要进行颜色反转——让当前结点的左子结点和右子结点都变为黑色，让指向当前结点的链接变为红色

4.向一个3-结点中插入新键

5.向底部的3-结点插入新键

###### 代码实现

```java
public class RedBlackTree<Key extends Comparable<Key>,Value> {
    //成员变量
    //根结点
    private Node root;
    //树中的元素个数
    private int N;
    //红色链接标识
    private static final boolean RED = true;
    //黑色链接标识
    private static final boolean BLACK = false;

    private class Node{
        //成员变量
        public Key key;
        public Value value;
        public Node left;
        public Node right;
        public boolean color;
        //结点类
        public Node(Key key,Value value,Node left,Node right,boolean color){
            this.key = key;
            this.value = value;
            this.left = left;
            this.right = right;
            this.color = color;
        }
    }
    //构造方法
    public RedBlackTree(){

    }

    //获取元素的个数
    public int size(){
        return N;
    }
    //判断结点x是否为红结点——即判断指向x的父链接是否是红链接
    private boolean isRed(Node x){
        if(x==null){
            return BLACK;
        }
        return x.color == RED;
    }
    //左旋
    private Node rotateLeft(Node h){
        //1.找到h结点的右子结点，标识为x
        Node x = h.right;
        //2.让x结点的左子结点成为h结点的右子结点
        h.right = x.left;
        //3.让h结点成为x结点的左子结点
        x.left = h;
        //4.让x的color属性等于h的color属性
        x.color = h.color;
        //5.让h的color属性变为RED
        h.color = RED;
        //6.返回父结点新指向的结点x
        return x;
    }
    //右旋
    private Node rotateRight(Node h){
        //1.获取h结点的左子结点，标识为x
        Node x = h.left;
        //2.让x结点的右子结点成为h结点的左子结点
        h.left = x.right;
        //3.让h结点成为x结点的右子结点
        x.right = h;
        //4.让x结点的color属性等于h结点的color属性
        x.color = h.color;
        //5.让h结点的color属性等于RED
        h.color = RED;
        return x;
    }
    //颜色反转
    private void flipColors(Node h){
        //让当前结点变为红色
        h.color = RED;
        //让当前结点的左子结点和右子结点都变为黑色
        h.left.color = BLACK;
        h.right.color = BLACK;
    }
    //向整个树中插入键值对
    public void put(Key key,Value value){
        root = put(root,key,value);
        //根结点的颜色总是黑色
        root.color = BLACK;
    }
    //put方法重载：向结点h的子树中插入键值对
    private Node put(Node h,Key key,Value value){
        //如果h为null，则创建一个红结点并返回
        if(h==null){
            return new Node(key,value,null,null,RED);
        }
        //如果h不为null，则比较key和h.key的大小
        int cmp = key.compareTo(h.key);
        if(cmp<0){
            //如果key小于h的键，则往左走（递归调用put方法）
            h.left = put(h.left,key,value);
        }else if(cmp>0){
            //如果key大于h的键，则往右走（递归调用put方法）
            h.right = put(h.right,key,value);
        }else{
            //如果key等于h的键，则发生值的替换
            h.value = value;
        }

        //插入完成后，进行红黑链接的变换
        //进行左旋：当当前结点的左子结点为黑色，右子结点为红色时，进行左旋
        if(isRed(h.right) && !isRed(h.left)){
            h = rotateLeft(h);
        }
        //进行右旋：当当前结点的左子结点和左子结点的左子结点都为红色时，进行右旋
        if(isRed(h.left) &&isRed(h.left.left)){
            h = rotateRight(h);
        }
        //颜色反转：当当前结点的左子结点和右子结点都为红色时，发生颜色反转
        if(isRed(h.right) && isRed(h.left)){
            flipColors(h);
        }
        return h;
    }
    //在整个树中获取键为key对应的值
    public Value get(Key key){

        return get(root,key);
    }
    //get方法重载：在子树h中获取键为key对应的值
    private Value get(Node h,Key key){
        if(h==null){
            return null;
        }
        int cmp = key.compareTo(h.key);
        if(cmp<0){
            return get(h.left,key);
        }else if(cmp>0){
            return get(h.right,key);
        }else{
            return h.value;
        }
    }

}
```

### B树

#### 定义

B树中允许一个结点含有多个键，我们选一个参数M来构造一个B树，这个树可以称作M阶的B树

#### 特点

1. 每个结点最多有M-1个key，并且以升序排列
2. 每个结点最多有M个子结点
3. 根结点最少有2个子结点

**B树也是自底向上生长的**

**应用**

**B树在磁盘文件中应用，可以极大提高从磁盘中读取数据的效率**

### B+树

#### 特点

B+树是对B树的变形，它与B树的差异在于：

1. 非叶结点只具有索引作用，也就是说，非叶节点只存储key，不存储value
2. 树的所有叶子结点构成一个有序链表，可以按照key的次序遍历全部数据

B+树与B树对比：

[B+]: https://www.bilibili.com/video/BV1iJ411E7xW?p=135&amp;spm_id_from=pageDriver	"B+树"

#### 应用

**利用B+树建立索引，大大提高数据库中的查询效率——单条查询、区间查询**

### 并查集

#### 用途

1. 判断2个元素是否属于同一个组
2. 合并两个不同的组

#### 特点

1. 并查集中可以包含多个组
2. 每个组都唯一地对应一棵树
3. 每个组中每一个元素都唯一地对应该树中唯一的结点，且这些结点没有明确的父子级关系
4. 多个组对应的树之间没有任何的联系

#### 代码实现

```java
public class UF {
    //记录元素和所在组的标识
    private int[] eleAndGroup;
    //记录并查集中分组的个数
    private int count;

    public UF(int N){
        //初始化分组个数，默认情况下，我们有N个结点元素，这些结点元素独立成组，共有N个分组
        this.count = N;
        //初始化eleAndGroup数组
        this.eleAndGroup = new int[N];
        //初始化eleAndGroup中的元素，让索引成为数组中存取的元素，
        //这些元素代表着分组的标识，与它们相对应的索引代表着每个结点所存的元素
        for (int i = 0; i < eleAndGroup.length; i++) {
            eleAndGroup[i] = i;
        }

    }
    //获取当前并查集中数据的组数
    public int count(){
        return count;
    }
    //查找元素p所在分组的标识符
    public int find(int p){
        return eleAndGroup[p];
    }
    //判断并查集中的元素p和元素q是否在同一个分组中
    public boolean connected(int p,int q){
        return find(p)==find(q);
    }
    //把元素p所在的分组和元素q所在的分组合并
    public void union(int p,int q){
        //判断元素p和元素q是不是在同一组，如果在，则结束运行
        if(connected(p,q)){
            return;
        }
        //找到元素p所在组的标识符
        int pGroup = find(p);
        //找到元素q所在组的标识符
        int qGroup = find(q);
        //合并组：把所有标识符为“元素p所在标识符”的元素的标识符全部修改为“元素q所在的标识符”
        for (int i : eleAndGroup) {
            if(i==pGroup){
                i = qGroup;
            }
        }
        //合并完成后，分组个数-1
        count--;
    }

}
```

**时间复杂度**

find：O(1)

union：O(n)

#### 算法优化

```java
//查找元素p所在分组的标识符
public int find(int p){
    while(true){
        if(p == eleAndGroup[p]){
            return p;
        }
        p = eleAndGroup[p];

    }
}
//把元素p所在的分组和元素q所在的分组合并
public void union(int p,int q){
    //先判断元素p和元素q是否在同一组
    if(connected(p,q)){
        return;
    }

    int pRoot = find(p);
    int qRoot = find(q);
    //合并元素p和元素q
    eleAndGroup[pRoot] = qRoot;
    //元素个数-1
    this.count--;
}
```

**时间复杂度**

find：O(n)

union：O(1)

#### 路径压缩

```java
//把元素p所在的分组和元素q所在的分组合并
public void union(int p,int q){
    //先判断元素p和元素q是否在同一组
    if(connected(p,q)){
        return;
    }

    int pRoot = find(p);
    int qRoot = find(q);
    //合并元素p和元素q
    //判断pRoot这个根结点对应的树中的元素个数和qRoot对应的树中的元素个数谁大
    if(sz[pRoot]<sz[qRoot]){
        //把小树合并到大树上
        //让pRoot的父结点变为qRoot，也就是让eleAndGroup数组中索引为pRoot的元素变为qRoot
        eleAndGroup[pRoot] = qRoot;
        //合并结束后，让大树的根结点对应的树的元素个数加上小树对应的元素个数
        sz[qRoot] += sz[pRoot];
    }else{
        eleAndGroup[qRoot] = pRoot;
        sz[pRoot] += sz[qRoot];
    }

    this.count--;
}
```

#### 案例—畅通工程

```java
package test;
import uf.UF_Tree_Weighted;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Traffic_Projrct_Test {
    public static void main(String[] args) throws IOException {
        //构建一个缓冲读取流BufferedReader
        BufferedReader  br = new BufferedReader(new InputStreamReader(Traffic_Projrct_Test.class.getClassLoader().getResourceAsStream("traffic_project.txt")));
        //读取第一行数据20
        int totalNumber = Integer.parseInt(br.readLine());
        //构建一个并查集对象
        UF_Tree_Weighted uf = new UF_Tree_Weighted(totalNumber);
        //读取第二行数据7
        int roadNumber = Integer.parseInt(br.readLine());
        //循环读取7条数据
        for(int i=1;i<=roadNumber;i++){
            String line = br.readLine();
            String[] str = line.split(" ");
            int p = Integer.parseInt(str[0]);
            int q = Integer.parseInt(str[1]);
            uf.union(p,q);
        }

        int roads = uf.count() - 1;

        System.out.println("还需要修建"+roads+"条道路，才能实现畅通");

        //获取当前并查集中分组的数量-1，就可以得到还需要修建的道路的数量
    }
}
```

### 图

#### 定义

由**一组顶点**和**一组连接两个顶点的边**构成

**特殊的图**：

1. 自环：边从一个顶点指向自身
2. 平行边：连接两个顶点的多条边

#### 分类

1. 无向图：边只连接两个顶点，没有方向
2. 有向图：边不仅连接两个顶点，还有方向

#### 术语

**相邻顶点：**两个顶点通过一条边相连通，这两个顶点就叫相邻顶点（或者称**这条边依附于这两个顶点**）

**度：**顶点的度是指依附于该顶点的边的个数

**子图：**由图中某些顶点和依附于这些顶点的边组成的图就是该图的子图

**路径：**由一个顶点到达另一个顶点所经过的顶点（包含起点和终点）

**环：**至少包含一条边的**起点和终点相同**的路径

**连通图：**图中任意两个顶点都是相互连通的（直接或间接连通）

**连通子图：非连通图中的**连通部分的图

#### 存储结构

##### 邻接矩阵

**思路：**用二维数组int[V] [V]来表示图

1. 用**二维数组的索引表示顶点**，
2. 用**二维数组中存储的值来表示两个顶点是否连接**，如果连接，则存储1，如果没有连接，则存储0

**注意：**如果是无向图，则int[1] [2] = int [2] [1]=1，表示顶点1和2之间有边相连，且是同一条边。

**问题：**邻接矩阵的空间复杂度是V^2，占用内存比较大

##### 邻接表

###### 思路：

1. 创建一个数组，用数组的索引来表示顶点
2. 每个索引处存储一个队列（Queue对象），**这个队列里面存储着所有与该索引代表的顶点相邻的顶点**

###### 实现

```java
public class Graph {
    //成员变量：顶点数目、边的数目、邻接表
    //顶点数目
    private int V;
    //边的数目
    private int E;
    //邻接表
    private Queue<Integer>[] adj;
    //构造方法
    public Graph(int V){
        //初始化顶点数量
        this.V = V;
        //初始化边的数量
        this.E = 0;
        //初始化数组adj
        this.adj = new Queue[V];
        //初始化adj数组每个索引处所存的值
        for (int i = 0; i < adj.length; i++) {
            adj[i] = new Queue<Integer>();
        }

    }
    //成员方法
    //获取顶点数目
    public int V(){
        return V;
    }
    //获取边的数目
    public int E(){
        return E;
    }
    //向图中添加一条边（在顶点v和顶点w之间）
    public void addEdge(int v,int w){
        //在无向图中，边没有方向，因此既可以说是顶点v指向顶点w的边，也可以说是顶点w指向顶点v的边
        //所以既要把w添加在v的邻接表中，也要把v添加在w的邻接表中
        adj[v].enqueue(w);
        adj[w].enqueue(v);
        //边的个数+1
        E++;
    }
    //获取和顶点v相邻的所有顶点
    public Queue<Integer> adj(int v){
        return adj[v];
    }


}
```

##### 深度优先搜索

**思路：**当发现一个结点既有子结点，又有兄弟结点的时候，**先去纵深搜索它的子结点**，当把它的子结点搜索完毕后，再回过头来搜索它的兄弟结点

```java
public class DepthFirstSearch {
    //成员变量
    //marked数组：索引代表顶点，值代表当前索引代表的顶点是否被搜索，如果是true，则已经被搜索
    private boolean[] marked;
    //记录有多少个顶点与s顶点相通
    private int count;

    //构造出深度优先搜索对象，使用深度优先搜索找出G图中所有与顶点s相通的顶点
    public DepthFirstSearch(Graph G,int s){
        this.marked = new boolean[G.V()];
        this.count = 0;
        dfs(G,s);
    }

    //使用深度优先搜索，找出G图中所有与顶点v相通的顶点
    private void dfs(Graph G,int v){
        //先把顶点v标记为已经搜索过
        marked[v] = true;

        //找到顶点v的邻接表，对邻接表进行遍历，获得每一个与顶点v相邻的结点
        Queue<Integer> queue = G.adj(v);
        for (Integer w : queue) {
            //如果该结点没有被搜索过，那么就对它递归调用dfs进行深度优先搜索
            if(!marked[w]){
                dfs(G,w);
            }
        }
        //每经过一次递归调用深度优先搜索，与顶点v相通的顶点数量count++
        count++;
    }

    //判断w顶点与s顶点是否相通
    public boolean marked(int w,int s){
        //如果在深度优先搜索中，w被搜索过了，即marked[w]为true，则相通
        return marked[w];
    }

    //获取与顶点s相通的所有顶点的总数
    public int count(){
        return count;
    }


}
```

##### 广度优先搜素

**思路：**在搜索时，如果遇到一个结点，既有子结点，又有兄弟结点，先找兄弟结点，再找子结点。

**代码：**

```java
package graph;

import Linear.Queue;

public class BreadthFirstSearch {
    //索引代表顶点，值表示当前顶点是否被搜索
    private boolean[] marked;
    //记录有多少个顶点与s结点相通
    private int count;
    //用来存储待搜索邻接表的点
    private Queue<Integer> waitSearch;

    //构造广度优先搜索对象，使用广度优先搜索找出G图中s顶点的所有相通顶点
    public BreadthFirstSearch(Graph G,int s) {
        this.marked = new boolean[G.V()];
        this.count = 0;
        this.waitSearch = new Queue<Integer>();

        bfs(G,s);
    }

    //使用广度优先搜索，找出G图中所有与顶点v相通的结点
    public void bfs(Graph G,int v){
        //把结点v标记为已搜索
        marked[v] = true;
        //让顶点v进入到队列中，待搜索
        waitSearch.enqueue(v);
        //只要队列不为空，就弹出一个顶点——while循环
        while(!waitSearch.isEmpty()){
            Integer pop = waitSearch.dequeue();
            //由弹出的顶点，得到与这个顶点对应的邻接表中的顶点
            for (Integer w : G.adj(pop)) {
                //如果邻接表中的顶点没有被搜索过，则对它递归调用广度遍历搜索
                if(!marked[w]){
                    bfs(G,w);
                }
            }
        }

        //循环结束后，与v相通的结点个数+1
        count++;
    }
    //判断w顶点与s顶点是否相通
    public boolean marked(int w){
        return marked[w];
    }

    //获取与顶点s相通的所有顶点的总数
    public int count(){
        return count;
    }
}
```

##### 畅通工程续

```java
package test;

import graph.DepthFirstSearch;
import graph.Graph;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Traffic_Project_Test2 {
    public static void main(String[] args) throws IOException {
        //构建一个缓冲输入流对象BufferedReader
        BufferedReader br = new BufferedReader(new InputStreamReader(Traffic_Projrct_Test.class.getClassLoader().getResourceAsStream("traffic_project.txt")));

        //读取第一行数据20
        int cityNumber = Integer.parseInt(br.readLine());
        //构建一个Graph对象
        Graph G = new Graph(cityNumber);
        //读取第二行数据7
        int roadNumber = Integer.parseInt(br.readLine());
        //循环读取有限行（7）数据，调用addEdge方法给数据中的结点添加边
        for (int i = 1; i <= roadNumber; i++) {
            String line = br.readLine();
            String[] str = line.split(" ");
            int v = Integer.parseInt(str[0]);
            int w = Integer.parseInt(str[1]);
            G.addEdge(v,w);
        }
        //构建一个深度优先搜索对象，起点设置为顶点9
        DepthFirstSearch search = new DepthFirstSearch(G, 9);
        //看顶点10和顶点8与顶点9是否相通
        System.out.println("顶点10和顶点9是否相通："+search.marked(10));
        System.out.println("顶点8和顶点9是否相通："+search.marked(8));
    }
}
```

##### 路径查找

```java
package graph;

import Linear.Stack;

public class DepthFirstPaths {
    //索引代表顶点，值代表当前顶点是否已经被搜索
    private boolean[] marked;
    //起点
    private int s;
    //索引代表顶点，值代表从起点s到当前顶点路径上的最后一个顶点
    private int[] edgeTo;

    //构造深度优先搜索对象，使用深度优先搜索找出G图中起点为s的所有路径
    public DepthFirstPaths(Graph G,int s){
        this.marked = new boolean[G.V()];
        this.s = s;
        this.edgeTo = new int[G.V()];
        //使用深度优先搜索，把从起点s到G图中任意一个连通的顶点的路径保存在edgeTo数组中
        dfs(G,s);
    }
    //使用深度优先搜索找到G图中所有与s起点相通的顶点（只要这个顶点被搜索过，即marked[顶点]=true，那么这个顶点就和起点相通）
    //同时通过edgeTo数组的记录，我们就可以把从起点s到G图中任何一个连通顶点的路径保存在edgeTo数组中
    private void dfs(Graph G,int v){
        //先把v标记为已搜索
        marked[v] = true;
        //循环遍历v的邻接表，获取每一个与v相邻的顶点，
        for (Integer w : G.adj(v)) {
            // 判断每一个顶点是否被搜索过，如果没有被搜索，则递归调用深度优先搜索（先要把edgeTo数组把路径的上一个元素保存起来）
            if(!marked[w]){
                edgeTo[w] = v;
                dfs(G,w);
            }
        }
    }
    //判断s顶点到v顶点是否存在路径
    public boolean hasPathTo(int v){
        //如果v被搜索过，就有路径
        return marked[v];
    }

    //找出从起点s到顶点v的路径（就是该路径经过的顶点）
    public Stack<Integer> pathTo(int v){
        if(!hasPathTo(v)){
            return null;
        }
        //创建一个栈，把路径中的所有顶点保存到栈中
        Stack<Integer> path = new Stack<>();

        for(int x=v;x!=s;x=edgeTo[x]){
            path.push(x);
        }
        path.push(s);
        return path;
    }
}

```

### 有向图

#### 定义

由一系列顶点和有方向的边构成的图

#### 术语

**出度：**从一个指出的边的条数，就是该顶点的出度

**入度：**指向一个顶点的边的条数，就是该顶点的入度

**有向路径：**由一系列顶点构成，且每个顶点都能存在一条有向边，指向序列中它的下一个顶点

**有向环：**是一条至少包含一条边，且起终点相同的有向路径

#### 顶点之间的关系

1. 两个顶点没有边相连
2. 存在由顶点v指向顶点w的边，v->w
3. 存在由顶点w指向顶点v的边，w->v
4. 顶点v和顶点w相互指向

#### 代码实现

```java
package graph;

import Linear.Queue;

public class Digraph {
    //顶点数目
    private int V;
    //边的数目
    private int E;
    //邻接表
    private Queue<Integer>[] adj;
    //构造方法
    public Digraph(int V){
        //初始化顶点数目
        this.V = V;
        //初始化边的数目
        this.E = 0;
        //初始化邻接表
        this.adj = new Queue[V];
        for (int i = 0; i < adj.length; i++) {
            adj[i] = new Queue<Integer>();
        }
    }

    //获取顶点数目
    public int V(){
        return V;
    }

    //获取边的数目
    public int E(){
        return E;
    }
    //向有向图中添加一条边v->w
    public void addEdge(int v,int w){
        //因为边是有向的，所以只需要让w出现在v的邻接表中即可
        //由此可以看出，有向图中相邻顶点的含义是：某顶点直接指出的顶点，是该结点的相邻结点（指入的不算）
        adj[v].enqueue(w);
        //边的个数+1
        E++;
    }

    //获取由v指出的边连接的所有顶点
    public Queue<Integer> adj(int v){
        return adj[v];
    }

    //返回该图的反向图
    public Digraph reverse(){
        //构造一个新的Digraph对象
        Digraph r = new Digraph(V);

        //给这个新的图添加与原图相反的边
        //遍历原图中的每一个顶点，获取每个顶点对应的邻接表，
        for(int v=0;v<V;v++){
            for (Integer w : adj[v]) {
                //继续遍历每个顶点对应的邻接表，获得相邻顶点，给r添加由相邻顶点指向原顶点的边
                r.addEdge(w,v);
            }
        }

        return r;
    }
}
```

#### 拓扑排序

##### 定义

给定一幅有向图，对图中的顶点进行排序，使得**所有的有向边均从排在前面的元素指向排在后面的元素**，这样就使得顶点能够按照优先级排序，这种排序就叫拓扑排序

##### 条件

图中没有有向环

##### 判断图中是否有环

```java
package graph;

import graph.Digraph;

public class DirectedCycle {
    //索引代表顶点，值代表当前顶点是否已经被搜索
    private boolean[] marked;
    //记录图中是否有环
    private boolean hasCycle;
    //索引代表顶点，使用栈的思想，记录当前顶点有没有已经处于正在被搜索的有向路径上
    private boolean[] onStack;

    //创建一个检测环对象，检测图G中是否有环
    public DirectedCycle(Digraph G){
        //初始化marked数组
        this.marked = new boolean[G.V()];
        //初始化hasCycle
        this.hasCycle = false;
        //初始化onStack数组
        this.onStack = new boolean[G.V()];
        
        //以图中的每一个顶点为起点，进行深度优先搜索，判断整个图中是否有环
        for(int v=0;v<G.V();v++){
            dfs(G,v);
        }
    }

    //基于深度优先搜素，检测图G中是否有环
    private void dfs(Digraph G,int v){
        //先把顶点v标记为已搜索
        marked[v] = true;
        //把顶点v入栈
        onStack[v] = true;
        //对顶点v进行深度优先搜索：
        //从顶点v出发，得到邻接表中的相邻顶点，纵深搜索（但是只把该次搜索的起点v入栈）
        //每次搜索一个结点，都检查这个顶点在不在栈中，如果在，那么就是一次搜索路径的起终点相同，检测到了环
        //对顶点v进行深度优先搜索——遍历v的邻接表，得到每一个相邻顶点，判断相邻顶点是否已经被搜索，如果没有则递归调用深度优先搜索
        for (Integer w : G.adj(v)) {
            if(!marked[w]){
                dfs(G,w);
            }

            //如果相邻顶点已经被搜索，则检查该顶点是否出现在栈中，如果出现在栈中，说明检测到了环
            if(onStack[w]){
                hasCycle = true;
                return;
            }
        }
        
        //把顶点v出栈
        onStack[v] = false;
    }

    //判断图是否有环
    public boolean hasCycle(){
        return hasCycle;
    }
}
```

##### 基于深度优先的顶点排序

```java
package graph;

import Linear.Stack;

public class DepthFirstOrder {

    //索引代表顶点，值代表当前顶点是否已经被搜索
    private boolean[] marked;
    //使用栈，存储顶点序列
    private Stack<Integer> reversePost;
    //创建一个基于深度优先搜索的深度优先排序对象，生成顶点线性序列
    public DepthFirstOrder(Digraph G){
        //初始化marked数组
        this.marked = new boolean[G.V()];
        //初始化reversePost对象
        this.reversePost = new Stack<Integer>();

        //遍历每一个顶点，以每一个顶点为入口进行深度优先搜索，生成顶点线性序列
        for(int v=0;v<G.V();v++){
            if(!marked[v]){
                dfs(G,v);
            }
        }
    }

    //基于深度优先搜索，生成顶点线性序列
    private void dfs(Digraph G,int v){
        //把v标记为已搜索
        marked[v] = true;
        //对顶点v进行深度优先搜索
        //遍历v的邻接表，获取每一个相邻顶点，如果该顶点没有被搜索过，就对它进行递归深度优先搜索
        for (Integer w : G.adj(v)) {
            if(!marked[w]){
                dfs(G,w);
            }
        }
        //搜索完毕后，将顶点v入栈
        reversePost.push(v);
    }
    //获取顶点线性序列
    public Stack<Integer> reversePost(){
        return reversePost;
    }

}
```

##### 拓扑排序

```java
package graph;

import Linear.Stack;

public class TopoLogical {
    //顶点的拓扑排序
    private Stack<Integer> order;
    //构造拓扑排序对象
    public TopoLogical(Digraph G){
        //创建一个检测环对象，用它判断传入的图G是否有环，如果没有环，则对图G进行顶点排序
        //顶点排序：先要创建一个顶点排序对象
        DirectedCycle cycle = new DirectedCycle(G);
        if(!cycle.hasCycle()){
            DepthFirstOrder depthFirstOrder = new DepthFirstOrder(G);
            order = depthFirstOrder.reversePost();
        }
    }
    //判断图G是否有环
    public boolean isCycle(){
        return order==null;
    }

    //获取拓扑排序的所有顶点
    public Stack<Integer> order(){
        return order;
    }
}
```

### 加权无向图

#### 定义

在无向图中，给每条边关联了一个权重或者成本

#### 用途

用于解决最小成本问题

#### 代码实现

```java
package graph;

import Linear.Queue;

public class EdgeWeightedGraph {
    //成员变量
    //记录顶点数量
    private final int V;
    //记录边的数量
    private int E;
    //邻接表
    private Queue<Edge>[] adj;

    //构造方法：创造一个含有V个顶点的空加权无向图
    public EdgeWeightedGraph(int V){
        this.V = V;
        this.E = 0;
        this.adj = new Queue[V];

        for (int i = 0; i < adj.length; i++) {
            adj[i] = new Queue<Edge>();
        }
    }
    //获取图中顶点的数量
    public int V(){
        return V;
    }
    //获取图中边的数量
    public int E(){
        return E;
    }

    //向加权无向图中添加一条边
    public void addEdge(Edge e){
        //因为是无向图，所以这条边应该既出现在它的两个顶点的邻接表中
        //获取这条边的一个顶点
        int v = e.either();
        //获取这条边的另外一个顶点
        int w = e.other(v);
        //把这条边添加到这两个顶点的邻接表中
        adj[v].enqueue(e);
        adj[w].enqueue(e);
    }
    //获取和顶点v关联的所有边
    public Queue<Edge> adj(int v){
        return adj[v];
    }
    //获取加权无向图的所有边
    public Queue<Edge> edges(){
        //创建一个Queue<Edge>，保存所有的边
        Queue<Edge> allEdges = new Queue<Edge>();
        //遍历所有的顶点，获取每个顶点的邻接表，遍历邻接表，把邻接表中该顶点的关联边添加到该数组中
        for(int v=0;v<V;v++){
            for (Edge e : adj[v]) {
                int w = e.other(v);
                if(v<w){
                    allEdges.enqueue(e);
                }
            }
        }
        //因为是无向图，所以每条边会被添加两遍
        //要想让一条边只被添加一次，在添加之前，比较该边的某个顶点比另一个顶点的大小，如果小，则添加（保证了只添加一次）
        return allEdges;
    }
}
```

### 最小生成树

#### 定义

图的生成树是一个包含其**所有顶点**的**无环**连通子图

#### 处理前提

1. 只处理连通图
2. 所有边的权重都是不相同的

#### 树的性质

1. 用一条边连接树中任意两个顶点，会得到一个新环
2. 从树中任意删除一条边，将得到两棵独立的树

#### 切分定理

**切分：**将图中的所有顶点按照一定的规则划分成**两个非空且没有交集**的集合

**横切边：**连接分属于两个**不同集合的两个顶点**的边叫做横切边

**切分定理：**在一幅加权无向图中，给定任意的切分，**一组横切边**中**权重最小的**一定属于图中的最小生成树

**注意：**一次切分产生的多个横切边中，权重最小的边并不是所有的横切边中唯一属于图的最小生成树的边。

#### 贪心算法

##### 定义

**使用切分定理找到最小生成树的一条边，不断地重复直到找到最小生成树的所有边**

如果有V个顶点，那么需要找到V-1条边，就可以表示该图的最小生成树

#### Prim算法

##### 切分规则

把在最小生成树中的顶点划为一个集合，不在最小生成树中的顶点划为另一个集合

##### 代码实现

```java
package graph;

import Linear.Queue;
import priority.IndexMinPriorityQueue;

public class PrimMST {
    //成员变量
    //索引代表顶点，值代表当前顶点和最小生成树之间的最短边
    private Edge[] edgeTo;
    //索引代表顶点，值代表当前顶点和最小生成树之间的最短边的权重
    private double[] distTo;
    //索引代表顶点，值代表当前顶点是否在最小生成树中，如果在，则值为true；否则为false
    private boolean[] marked;
    //存放最小生成树中顶点与非树中顶点之间的有效横切边
    private IndexMinPriorityQueue<Double> pq;

    //构造方法
    //根据一幅加权无向图，创建最小生成树计算对象
    public PrimMST(EdgeWeightedGraph G){
        this.edgeTo = new Edge[G.V()];
        this.distTo = new double[G.V()];
        this.marked = new boolean[G.V()];
        this.pq = new IndexMinPriorityQueue<Double>(G.V());

        //默认让顶点0进入到树中，但是树中只有0这一个顶点，因此，0顶点默认没有和其他的顶点相连，所以distTo对应位置处的值存储0.0
        distTo[0] = 0.0;
        pq.insert(0,0.0);

        //遍历索引最小优先队列，拿到最小横切边对应的顶点，把该顶点加入到最小生成树中
        while(!pq.isEmpty()){
            visit(G,pq.delMin());
        }
    }
    //成员方法
    //将顶点v添加到最小生成树中，并更新数据
    public void visit(EdgeWeightedGraph G,int v){
        //把顶点v添加到最小生成树中
        marked[v] = true;
        //更新数据
        for (Edge e : G.adj(v)) {
            int w = e.other(v);
            if(marked[w]){
                continue;
            }

            if(e.weight()<distTo[w]){
                edgeTo[w] = e;
                distTo[w] = e.weight();

                if(pq.contains(w)){
                    pq.changeItem(w,e.weight());
                }else{
                    pq.insert(w, e.weight());
                }
            }
        }

    }

    //获取最小生成树的所有边
    public Queue<Edge> edges(){
        return null;
    }
}
```

#### Kruskal算法

##### 思路

1. 用一个最小优先队列来存储边，它会对边按照权重进行排序，循环调用delMin()方法，（删除并）获得队列中权重最小的边
2. 使用并查集来保存所有的顶点
3. 检查得到的每条边的两个顶点是否在同一棵树中
   1. 如果不在同一棵树，则为这两个顶点添加边；
   2. 如果在同一棵树中，则继续下一次循环，从队列中找出现有的权重最小的边
4. 直到所有的边都被检查完，我们得到的树就是最小生成树

##### 代码实现

```java
public class KruskalMST {
    //成员变量：
    //保存最小生成树的所有边
    private Queue<Edge> mst;
    //索引代表顶点，使用uf.connect(v,w)可以判断顶点v和w是否在同一棵树中，
    //如果不在同一棵树中，使用uf.union(v,w)可以把顶点v所在的树和顶点w所在的树合并
    private UF_Tree_Weighted uf;
    //存储图中所有的边，使用最小优先队列，对边按照权重进行排序
    private MinPriorityQueue<Edge> pq;

    //构造方法：根据一幅加权无向图，创建最小生成树计算对象
    public KruskalMST(EdgeWeightedGraph G){
        //初始化mst
        this.mst = new Queue<>();
        //初始化uf
        this.uf = new UF_Tree_Weighted(G.V());
        //初始化pq
        this.pq = new MinPriorityQueue<Edge>(G.E()+1);
            //初始化情况下，pq中应该存入所有的边——遍历所有的边，并逐一存入pq中
        for (Edge e : G.edges()) {
            pq.insert(e);
        }
        
        //从pq中获取权重最小的边，得到它的两个顶点，
        while(!pq.isEmpty() && mst.size()<G.V()-1){
            Edge min = pq.delMin();
            int v = min.either();
            int w = min.other(v);
            //如果这两个顶点在同一棵树中，则什么都不做，继续处理下一条权重最小的边
            if(uf.connected(v,w)){
                continue;
            }
            //如果这两个顶点不在同一棵树中，则把它们相连（让它们能在同一棵树中），同时把这条边添加到mst中
            uf.union(v,w);
            mst.enqueue(min);
        }
    }
    //获取最小生成树的所有边
    public Queue<Edge> edges(){
        return mst;
    }
}
```

### 加权有向图

#### 加权有向边

```java
public class DirectedEdge {
    //成员变量
    //起点
    private final int v;
    //终点
    private final int w;
    //有向边的权重
    private final double weight;

    //构造方法
    //通过顶点v，w，权重weight构造一个边对象
    public DirectedEdge(int v,int w,double weight){
        this.v = v;
        this.w = w;
        this.weight = weight;
    }

    //成员方法
    //获取有向边的起点
    public int from(){
        return v;
    }

    //获取有向边的终点
    public int to(){
        return w;
    }
    //获取有向边的权重
    public double weight(){
        return weight;
    }
}
```

#### 加权有向图

```java
public class EdgeWeightedDigraph {
    //成员变量
    //顶点数量
    private int V;
    //边的数量
    private int E;
    //邻接表：索引代表顶点，存储的值代表由该顶点指出的所有的边
    private Queue<DirectedEdge>[] adj;
    //构造方法
    //创建一个含有V个顶点的空的加权有向图
    public EdgeWeightedDigraph(int V){
        this.V = V;
        this.E = 0;
        this.adj = new Queue[V];
        for (int i = 0; i < adj.length; i++) {
            adj[i] = new Queue<DirectedEdge>();
        }
    }

    //获取图中顶点的个数
    public int V(){
        return V;
    }
    //获取图中边的个数
    public int E(){
        return E;
    }
    //向加权有向图中添加一条边
    public void addEdge(DirectedEdge e){
        //因为加权有向图中的边是有方向的，所以只要把边添加到它的起点的邻接表中即可
        //获取起点
        int v = e.from();
        //把边e添加到起点的邻接表中
        adj[v].enqueue(e);
        //边的个数+1
        E++;
    }
    //获取由顶点v指出的所有边
    public Queue<DirectedEdge> adj(int v){
        return adj[v];
    }
    //获取加权有向图的所有边
    public Queue<DirectedEdge> edges(){
        //创建一个Queue对象，用于保存最后的结果
        //遍历每一个顶点，得到该顶点的邻接表，遍历该邻接表，把表中的边添加到allEdges中
        Queue<DirectedEdge> allEdges = new Queue<>();
        for(int v=0;v<V;v++){
            for (DirectedEdge e : adj[v]) {
                allEdges.enqueue(e);
            }
        }

        return allEdges;
    }

}
```

### 最短路径

#### 定义

从起点到终点的总权重最小的路径

#### 性质

1. 路径是有方向的
2. 权重的实际含义就是成本，可以是距离、时间、花费
3. 只考虑连通图
4. 最短路径不一定唯一。即总权重最小的路径可以有多条，我们找出一条即可。

### 最短路径树

#### 定义

给定一幅加权有向图和一个起点s，找出**从起点s到图中其他所有顶点的最短路径**，最终这些路径共同构成一个**以s为根结点的有向树**，这个有向树就是最短路径树

#### 松弛技术

##### 边的松弛

1. 松弛边意味着**检查从s到w的最短路径是否是先要从s到达v，然后再从v到达w**
2. 如果是，则v-w这条边需要加入到最短路径树中，同时更新edgeTo和distTo的内容：
   1. edgeTo[w] = v->w这条有向边对象
   2. distTo[w] = distTo[v] + v->w这条边的权重
3. 如果不是，则忽略v->w这条边

##### 顶点的松弛

只要把**某个顶点指出的所有边都松弛**，那么这个顶点就松弛完毕

**思路：**要松弛顶点v，就遍历它的邻接表，获得由顶点v指出的每条边，逐一进行松弛，循环结束后，顶点v就被松弛。

##### 代码实现

```Java
package graph;

import Linear.Queue;
import priority.IndexMinPriorityQueue;

public class DijkstraSP {
    //索引代表顶点，值表示从顶点s到当前顶点的最短路径上的最后一条边
    private DirectedEdge[] edgeTo;
    //索引代表顶点，值表示从顶点s到当前顶点的最短路径的总权重
    private double[] distTo;
    //存放树中顶点与非树中顶点之间的有效横切边
    private IndexMinPriorityQueue<Double> pq;


    //根据一幅加权有向图G和顶点s，创建一个根结点为s的最短路径树对象
    public DijkstraSP(EdgeWeightedDigraph G,int s){
        //初始化edgeTo
        this.edgeTo = new DirectedEdge[G.V()];
        //初始化distTo
        this.distTo = new double[G.V()];
        for (int i = 0; i < distTo.length; i++) {
            distTo[i] = Double.POSITIVE_INFINITY;
        }
        //初始化pq
        this.pq = new IndexMinPriorityQueue<Double>(G.V());

        //找到图G中以顶点s为起点的最短路径树

        //默认让顶点s进入到最短路径树中
        distTo[s] = 0.0;
        pq.insert(s,0.0);;
        //遍历pq
        while(!pq.isEmpty()){
            relax(G,pq.delMin());
        }
    }
    //松弛图G中的顶点v
    private void relax(EdgeWeightedDigraph G,int v){
        //遍历顶点v的邻接表，获取到所有与顶点v相邻的边
        for (DirectedEdge edge : G.adj(v)) {
            //获取到该边的终点w
            int w = edge.to();

            //通过松弛技术，判断从起点s到顶点w的最短路径是否需要从顶点s先到达顶点v，然后再由顶点v到达顶点w
            if(distTo(v)+edge.weight()<distTo(w)){
                distTo[w] = distTo[v]+ edge.weight();
                edgeTo[w] = edge;

                //判断pq中是否已经存在顶点w，如果存在，则更新数据，如果不存在，则直接添加
                if(!pq.isEmpty()){
                    pq.changeItem(w,distTo(w));
                }else{
                    pq.insert(w,distTo(w));
                }

            }
        }
    }
    //获取从顶点s到顶点v的最短路径的总权重
    public double distTo(int v){
        return distTo[v];
    }

    //判断从顶点s到顶点v是否可达
    public boolean hasPathTo(int v){
        return distTo[v]<Double.POSITIVE_INFINITY;
    }
    //查询从起点s到顶点v的最短路径中所有的边
    public Queue<DirectedEdge> pathTo(int v){
        //判断从s到v是否可达，如果不可达，则返回null
        if(!hasPathTo(v)){
            return null;
        }
        //创建队列对象
        Queue<DirectedEdge> allEdges = new Queue<>();
        while(true){
            DirectedEdge e = edgeTo[v];
            if(e==null){
                break;
            }
            allEdges.enqueue(e);
            
            v = e.from();
        }
        return allEdges;
    }

}
```
