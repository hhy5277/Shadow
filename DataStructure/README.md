# 数据结构思维

### 第一章 接口

###### 1.为什么要有两种List

> Java中List接口为ArrayList, LinkedList都继承于List.哪一个更适合于特定的应用程序，取决于它最常执行的操作.对于一些应用，`LinkedList`更快；对于其他应用，`ArrayList`更快.<br>



###### 2.List接口

```java
public class ListClientExample {
    private List list;

    public ListClientExample() {
        list = new LinkedList();
    }

    private List getList() {
        return list;        
    }

    public static void main(String[] args) {
        ListClientExample lce = new ListClientExample();
        List list = lce.getList();
        System.out.println(list);
    }
}
```

> `ListClientExample`没有任何有用的东西，但它封装了`List`，并具有一个类的基本要素。也就是说，它包含一个`List`实例变量。

> **通过实例化（也就是创建）新的`LinkedList`，这个`ListClientExample`构造函数初始化`list`；读取器方法叫做`getList`，返回内部`List`对象的引用；并且`main`包含几行代码来测试这些方法。**

> 这个例子的要点是，它尽可能地使用`List`，避免指定`LinkedList`，`ArrayList`，除非有必要。例如，实例变量被声明为`List`，并且`getList`返回`List`，但都不指定哪种类型的列表。

> 如果你改变主意并决定使用`ArrayList`，你只需要改变构造函数; 你不必进行任何其他更改。

> 这种风格被称为基于接口的编程，或者更随意，“面向接口编程”。这里我们谈论接口的一般思想，而不是 Java 接口。<br>



### 第二章  算法分析

###### 1.简单算法分类

- 常数时间**O(1)**：不依赖于输入
- 线性**O(n)**:依赖于输入，并跟输入量大小成正比
- 平方**O(n^2)**:随着n的增长变为n^2

###### 2.简单排序

```javascript
public class SelectionSort {

    /**
     * Swaps the elements at indexes i and j.
     */
    public static void swapElements(int[] array, int i, int j) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }

    /**
     * Finds the index of the lowest value
     * starting from the index at start (inclusive)
     * and going to the end of the array.
     */
    public static int indexLowest(int[] array, int start) {
        int lowIndex = start;
        for (int i = start; i < array.length; i++) {
            if (array[i] < array[lowIndex]) {
                lowIndex = i;
            }
        }
        return lowIndex;
    }

    /**
     * Sorts the elements (in place) using selection sort.
     */
    public static void selectionSort(int[] array) {
        for (int i = 0; i < array.length; i++) {
            int j = indexLowest(array, i);
            swapElements(array, i, j);
        }
    }
}
```

swap()是常数级操作，时间恒定

indexLowest()复杂度，同n-start有关，属于线性关系

selectionSort()，需要算n(n-1)/2,属于平方关系<br>



### 第三章  ArrayList

###### 1.Add方法

单参方法(分析它的时间复杂度)

```js
@Override
    public boolean add(T element) {
        //make a bigger array and copy over the elements
        if (size >= array.length) {
            T[] bigger = new Object[array.length * 2];
            System.arraycopy(array,0,bigger,0,array.length);
            array = bigger;
        }
        array[size] = element;
        size++;
        return true;
    }
```

> 单参数版本很难分析。如果数组中存在**未使用的空间，那么它是常数时间**，但如果我们必须**调整数组的大小，它是线性的**，因为`System.arraycopy`所需的时间与数组的大小成正比.（既存在常数时间，又是线性的）

> 那么`add`是常数还是线性时间的？（先给结论，可以看作是常数的）

- 我们第一次调用`add`时，它会在数组中找到未使用的空间，所以它存储`1`个元素。
- 第二次，它在数组中找到未使用的空间，所以它存储`1`个元素。
- 第三次，我们必须调整数组的大小，复制`2`个元素，并存储`1`个元素。现在数组的大小是`4`。
- 第四次存储`1`个元素。
- 第五次调整数组的大小，复制`4`个元素，并存储`1`个元素。现在数组的大小是`8`。
- 接下来的`3`个添加储存`3`个元素。
- 下一个添加复制`8`个并存储`1`个。现在的大小是`16`。
- 接下来的`7`个添加复制了`7`个元素。

> 整理一下规律

- `4`次添加之后，我们储存了`4`个元素，并复制了两个。
- `8`次添加之后，我们储存了`8`个元素，并复制了`6`个。
- `16`次添加之后，我们储存了`16`个元素，并复制了`14`个。

> 现在你应该看到了规律：要执行`n`次添加，我们必须存储`n`个元素并复制`n-2`个。所以操作总数为`n + n - 2`，为`2 * n - 2`
>
> 为了得到每个添加的平均操作次数，我们将总和除以`n`；结果是`2 - 2 / n`。随着`n`变大，第二项`2 / n`变小。参考我们只关心`n`的最大指数的原则，我们可以认为`add`是常数时间的

>有时线性的算法平均可能是常数时间，这似乎是奇怪的。关键是我们每次调整大小时都加倍了数组的长度。这限制了每个元素被复制的次数。否则 - 如果我们向数组的长度添加一个固定的数量，而不是乘以一个固定的数量 - 分析就不起作用。

>这种划分算法的方式，通过计算一系列调用中的平均时间，称为摊销分析。你可以在 <http://thinkdast.com/amort> 上阅读更多信息。重要的想法是，**复制数组的额外成本是通过一系列调用展开或“摊销”的。**<br>

<br>

双参方法

```js
   @Override
    public void add(int index, T element) {
        if (index<0 || index>size) {
            throw IndexOutOfBoundsException;
        }

        //new element（扩容之需）
        add(element);

        //shift the other element
        for (int i=size-1; i>index; i--) {
            array[i] = array[i-1];
        }

        //put the new one in the right place
        array[index] = element;
    }
```

> 这个双参数的版本，叫做`add(int, E)`，它使用了单参数的版本，称为`add(E)`，它将新的元素放在最后。然后它将其他元素向右移动，并将新元素放在正确的位置

> 现在，如果`add(E)`是常数时间，那么`add(int, E)`呢？调用`add(E)`后，它遍历数组的一部分并移动元素。这个循环是线性的，除了在列表末尾添加的特殊情况中。**因此， `add(int, E)`是线性的**。<br>



###### 2.划分MyArrayList的方法

> 对于许多方法，我们不能通过测试代码来确定增长级别<br>



**常数级别**

> `get`中的每个东西都是常数时间的。所以`get`是常数时间

```java
public E get(int index) {
    if (index < 0 || index >= size) {
        throw new IndexOutOfBoundsException();
    }
    return array[index];
}
```

> `set`中的一切，包括`get`的调用都是常数时间，所以`set`也是常数时间
>
> set它不会显式检查数组的边界；它利用`get`，如果索引无效则引发异常

```java
public E set(int index, E element) {
    E old = get(index);
    array[index] = element;
    return old;
}
```

<br>

**线性级别**

>每次在循环中，`indexOf`调用`equals`，所以我们首先要划分`equals`

```java
public int indexOf(Object target) {
    for (int i = 0; i<size; i++) {
        if (equals(target, array[i])) {
            return i;
        }
    }
    return -1;
}
```

> **equals方法**

```java
private boolean equals(Object target, Object element) {
    if (target == null) {
        return element == null;
    } else {
        return target.equals(element);
    }
}
```

> 如果我们幸运，我们可能会立即找到目标对象，并在测试一个元素后返回。如果我们不幸，我们可能需要测试所有的元素。平均来说，我们预计测试一半的元素，所以这种方法被认为是**线性**的（除了在不太可能的情况下，我们知道目标元素在数组的开头）<br>



###### 3.问题规模

**RemoveAll**

```java
public boolean removeAll(Collection<?> collection) {
    boolean flag = true;
    for (Object obj: collection) {
        flag &= remove(obj);
    }
    return flag;
}
```

> 每次循环中，`removeAll`都调用`remove`，这是线性的。所以认为`removeAll`是二次的很诱人。但事实并非如此。

>在这种方法中，循环对于每个`collection`中的元素运行一次。如果`collection`包含`m`个元素，并且我们从包含`n`个元素的列表中删除，则此方法是`O(nm)`的。如果`collection`的大小可以认为是常数，`removeAll`相对于`n`是线性的。但是，如果集合的大小与`n`成正比，`removeAll`则是平方的。例如，如果`collection`总是包含`100`个或更少的元素， `removeAll`则是线性的。但是，如果`collection`通常包含的列表中的 1% 元素，`removeAll`则是平方的。

> **当我们谈论问题规模时，我们必须小心我们正在讨论哪个大小**。这个例子演示了算法分析的陷阱：对循环计数的诱人捷径。如果有一个循环，算法往往是 线性的。如果有两个循环（一个嵌套在另一个内），则该算法通常是平方的。不过要小心！**你必须考虑每个循环运行多少次**。如果所有循环的迭代次数与`n`成正比，你可以仅仅对循环进行计数之后离开。但是，如在这个例子中，**迭代次数并不总是与`n`成正比**，所以你必须考虑更多。