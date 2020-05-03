# 散列表

## <font color = blue>内容概要</font>

1. 散列表原理
2. 如何解决散列冲突问题

## <font color = blue>遗留问题</font>





### 散列思想

+ 散列表（Hash Table） 哈希表
+ 散列表用的是数组支持按照下标随机访问数据的特性，散列表其实就是数组的一种扩展，由数组演化而来。可以说，如果没有数组，就没有散列表。

+ 以运动员成绩为例，参赛选手的编号叫作**键**（key）或者**关键字**。用它来标识一个选手。把参赛编号转化为数组下标的映射方法就叫作**散列函数**（或“Hash 函数”“哈希函数”），而散列函数计算得到的值就叫作**散列值（或“Hash 值”“哈希值”）**。

  + 散列表用的就是数组支持按照下标随机访问的时候，时间复杂度是 O(1) 的特性。
  + 通过散列函数把元素的键值映射为下标，然后将数据存储在数组中对应下标的位置。
  + 当按照键值查询元素时，用同样的散列函数，将键值转化数组下标，从对应的数组下标的位置取数据

  ![](1.jpg)

​      

### 散列函数

+ 散列函数，顾名思义，它是一个函数。可以把它定义成 hash(key)，其中 key 表示元素的键值，hash(key) 的值表示经过散列函数计算得到的散列值。
+ 散列函数设计的三个基本要求
  + 散列函数计算得到的散列值是一个非负整数；
  + 如果 key1 = key2，那 hash(key1) == hash(key2)；
  + 如果 key1 ≠ key2，那 hash(key1) ≠ hash(key2)。
    + 其中，第一点理解起来应该没有任何问题。因为数组下标是从 0 开始的，所以散列函数生成的散列值也要是非负整数。
    + 第二点，相同的 key，经过散列函数得到的散列值也应该是相同的。
    + 第三点这个要求看起来合情合理，但是在真实的情况下，要想找到一个不同的 key 对应的散列值都不一样的散列函数，几乎是不可能的。即便像业界著名的MD5、SHA、CRC等哈希算法，也无法完全避免这种**散列冲突**。而且，因为数组的存储空间有限，也会加大**散列冲突**的概率。
    + 所以几乎无法找到一个完美的无冲突的散列函数，即便能找到，付出的时间成本、计算成本也是很大的，所以针对散列冲突问题，需要通过其他途径来解决。



### 解决散列冲突的方法

+ 开放寻址法（open addressing）

  + 开放寻址法的核心思想是，如果出现了散列冲突，就重新探测一个空闲位置，将其插入。一个比较简单的探测方法，**线性探测**（Linear Probing）。

  + 当往散列表中插入数据时，如果某个数据经过散列函数散列之后，存储位置已经被占用了，就从当前位置开始，依次往后查找，看是否有空闲位置，直到找到为止。如下图所示，这里面黄色的色块表示空闲位置，橙色的色块表示已经存储了数据。

    + 从图中可以看出，散列表的大小为 10，在元素 x 插入散列表之前，已经 6 个元素插入到散列表中。

    + x 经过 Hash 算法之后，被散列到位置下标为 7 的位置，但是这个位置已经有数据了，所以就产生了冲突。于是我们就顺序地往后一个一个找，看有没有空闲的位置，遍历到尾部都没有找到空闲的位置，于是我们再从表头开始找，直到找到空闲位置 2，于是将其插入到这个位置。

      ![](2.jpg)

  + 在散列表中查找元素的过程有点儿类似插入过程。

    + 通过散列函数求出要查找元素的键值对应的散列值，然后比较数组中下标为散列值的元素和要查找的元素。

    + 如果相等，则说明就是要找的元素；否则就顺序往后依次查找。

    + **如果遍历到数组中的空闲位置，还没有找到，就说明要查找的元素并没有在散列表中**。只要第一次遇见空闲位置，则说明要查找的元素不在散列表中。

      ![](3.jpg)

  + 散列表跟数组一样，不仅支持插入、查找操作，还支持删除操作。

    + 对于**使用线性探测法**解决冲突的散列表，删除操作稍微有些特别。不能单纯地把要删除的元素设置为空。

    + **在查找的时候，一旦通过线性探测方法，找到一个空闲位置，就可以认定散列表中不存在这个数据**。但是，如果这个空闲位置是后来删除的，就会导致原来的查找算法失效。本来存在的数据，会被认定为不存在。

    + 可以将删除的元素，特殊标记为 deleted。当线性探测查找的时候，遇到标记为 deleted 的空间，并不是停下来，而是继续往下探测。

      ![](4.jpg)

  + 线性探测法存在的问题。

    + 当散列表中插入的数据越来越多时，散列冲突发生的可能性就会越来越大，空闲位置会越来越少，线性探测的时间就会越来越久。

    + 极端情况下，可能需要探测整个散列表，所以最坏情况下的时间复杂度为 O(n)。同理，在删除和查找时，也有可能会线性探测整张散列表，才能找到要查找或者删除的数据。

    + 对于开放寻址冲突解决方法，除了线性探测方法之外，还有另外两种比较经典的探测方法，**二次探测**（Quadratic probing）和**双重散列**（Double hashing）。

      + 所谓二次探测，跟线性探测很像，线性探测每次探测的步长是 1，那它探测的下标序列就是 hash(key)+0，hash(key)+1，hash(key)+2……而二次探测探测的步长就变成了原来的“二次方”，也就是说，它探测的下标序列就是 $hash(key)+0, hash(key)+1^2,hash(key)+2^2$……
      + 所谓双重散列，意思就是不仅要使用一个散列函数。而且使用一组散列函数 hash1(key)，hash2(key)，hash3(key)……我们先用第一个散列函数，如果计算得到的存储位置已经被占用，再用第二个散列函数，依次类推，直到找到空闲的存储位置。

    + 不管采用哪种探测方法，当散列表中空闲位置不多的时候，散列冲突的概率就会大大提高。

    + 为了尽可能保证散列表的操作效率，一般情况下，会尽可能保证散列表中有一定比例的空闲槽位。

    + 用**装载因子**（load factor）来表示空位的多少。装载因子越大，说明空闲位置越少，冲突越多，散列表的性能会下降。

      ```
      散列表的装载因子=填入表中的元素个数/散列表的长度
      ```

      

+ 链表法

  + 链表法是一种更加常用的散列冲突解决办法，相比开放寻址法，它要简单很多。

  + 在散列表中，每个“桶（bucket）”或者“槽（slot）”会对应一条链表，所有散列值相同的元素都放到相同槽位对应的链表中。

    + 当插入的时候，只需要通过散列函数计算出对应的散列槽位，将其插入到对应链表中即可，所以插入的时间复杂度是 O(1)。
    + 当查找、删除一个元素时，同样通过散列函数计算出对应的槽，然后遍历链表查找或者删除。
    + 实际上，查找或删除的时间复杂度跟链表的长度 k 成正比，也就是 O(k)。对于散列比较均匀的散列函数来说，理论上讲，k=n/m，其中 n 表示散列中数据的个数，m 表示散列表中“槽”的个数。

    ![](5.jpg)



### 散列表查询效率

+ 散列表的查询效率并不能笼统地说成是 O(1)。它跟散列函数、装载因子、散列冲突等都有关系。如果散列函数设计得不好，或者装载因子过高，都可能导致散列冲突发生的概率升高，查询效率下降。
+ 在极端情况下，有些恶意的攻击者，很有可能通过精心构造的数据，使得所有的数据经过散列函数之后，都散列到同一个槽里。如果我们使用的是基于链表的冲突解决方法，那这个时候，散列表就会退化为链表，查询的时间复杂度就从 O(1) 急剧退化为 O(n)。
+ 应该设计一个可以应对各种异常情况的工业级散列表，来避免在散列冲突的情况下，散列表性能的急剧下降，并且能抵抗散列碰撞攻击。



### 如何设计散列函数？

+ 散列函数设计的好坏，决定了散列表冲突的概率大小，也直接决定了散列表的性能。好的散列函数具有以下特征：
  + 首先，散列函数的设计不能太复杂。过于复杂的散列函数，势必会消耗很多计算时间，也就间接的影响到散列表的性能。
  + 其次，散列函数生成的值要尽可能随机并且均匀分布，这样才能避免或者最小化散列冲突，而且即便出现冲突，散列到每个槽里的数据也会比较平均，不会出现某个槽内数据特别多的情况。



### 装载因子过大了怎么办？

+ 散列表的装载因子越大，说明散列表中的元素越多，空闲位置越少，散列冲突的概率就越大。不仅插入数据的过程要多次寻址或者拉很长的链，查找的过程也会因此变得很慢。

+ 对于没有频繁插入和删除的静态数据集合来说，很容易根据数据的特点、分布等，设计出完美的、极少冲突的散列函数，因为毕竟之前数据都是已知的。

+ 对于动态散列表来说，数据集合是频繁变动的，事先无法预估将要加入的数据个数，所以也无法事先申请一个足够大的散列表。随着数据慢慢加入，装载因子就会慢慢变大。当装载因子大到一定程度之后，散列冲突就会变得不可接受。这个时候，我们该如何处理呢？

  + 针对散列表，当装载因子过大时，我们也可以进行动态扩容，重新申请一个更大的散列表，将数据搬移到这个新散列表中。假设每次扩容我们都申请一个原来散列表大小两倍的空间。

  + 如果原来散列表的装载因子是 0.8，那经过扩容之后，新散列表的装载因子就下降为原来的一半，变成了 0.4。

  + 针对数组的扩容，数据搬移操作比较简单。但是，针对散列表的扩容，数据搬移操作要复杂很多。因为散列表的大小变了，数据的存储位置也变了，所以需要通过散列函数重新计算每个数据的存储位置。如下图所示，在原来的散列表中，21 这个元素原来存储在下标为 0 的位置，搬移到新的散列表中，存储在下标为 7 的位置。

    ![](6.jpg)

  + 对于支持动态扩容的散列表，插入操作的时间复杂度分析：

    + 插入一个数据，最好情况下，不需要扩容，最好时间复杂度是 O(1)。
    + 最坏情况下，散列表装载因子过高，启动扩容，需要重新申请内存空间，重新计算哈希位置，并且搬移数据，所以时间复杂度是 O(n)。
    + 用摊还分析法，均摊情况下，时间复杂度接近最好情况，就是 O(1)。

  + 装载因子的设置：

    + 实际上，对于动态散列表，随着数据的删除，散列表中的数据会越来越少，空闲空间会越来越多。如果对空间消耗非常敏感，可以在装载因子小于某个值之后，启动动态缩容。当然，如果更加在意执行效率，能够容忍多消耗一点内存空间，那就可以不用费劲来缩容了。
    + 当散列表的装载因子超过某个阈值时，就需要进行扩容。装载因子阈值需要选择得当。如果太大，会导致冲突过多；如果太小，会导致内存浪费严重。
    + 装载因子阈值的设置要权衡时间、空间复杂度。如果内存空间不紧张，对执行效率要求很高，可以降低负载因子的阈值；相反，如果内存空间紧张，对执行效率要求又不高，可以增加负载因子的值，甚至可以大于 1。



### 如何避免低效地扩容？

+ 动态扩容时插入数据

  + 大部分情况下，动态扩容的散列表插入一个数据都很快，但是在特殊情况下，当装载因子已经到达阈值，需要先进行扩容，再插入数据。这个时候，插入数据就会变得很慢，甚至会无法接受。

  + 一个极端的例子，如果散列表当前大小为 1GB，要想扩容为原来的两倍大小，那就需要对 1GB 的数据重新计算哈希值，并且从原来的散列表搬移到新的散列表，听起来就很耗时，是不是？如果我们的业务代码直接服务于用户，尽管大部分情况下，插入一个数据的操作都很快，但是，极个别非常慢的插入操作，也会让用户崩溃。这个时候，“一次性”扩容的机制就不合适了。

  + 为了解决一次性扩容耗时过多的情况，可以将扩容操作穿插在插入操作的过程中，分批完成。当装载因子触达阈值之后，只申请新空间，但并不将老的数据搬移到新散列表中。

  + 当有新数据要插入时，将新数据插入新散列表中，并且从老的散列表中拿出一个数据放入到新散列表。每次插入一个数据到散列表，都重复上面的过程。经过多次插入操作之后，老的散列表中的数据就一点一点全部搬移到新散列表中了。这样没有了集中的一次性数据搬移，插入操作就都变得很快了。

    ![](7.jpg)

  + 对于查询操作，为了兼容了新、老散列表中的数据，先从新散列表中查找，如果没有找到，再去老的散列表中查找。

  + 通过这样均摊的方法，将一次性扩容的代价，均摊到多次插入操作中，就避免了一次性扩容耗时过多的情况。这种实现方式，任何情况下，插入一个数据的时间复杂度都是 O(1)。





### 如何选择冲突解决方法？

+ 解决方法

  + 两种主要的散列冲突的解决办法，开放寻址法和链表法。这两种冲突解决办法在实际的软件开发中都非常常用。比如，Java 中 LinkedHashMap 就采用了链表法解决冲突，ThreadLocalMap 是通过线性探测的开放寻址法来解决冲突。

+ 开放寻址法

  + 优点
    + 开放寻址法不像链表法，需要拉很多链表。
    + 散列表中的数据都存储在数组中，可以有效地利用 CPU 缓存加快查询速度。而且，这种方法实现的散列表，序列化起来比较简单。链表法包含指针，序列化起来就没那么容易。序列化在很多场合都会用到的。
  + 缺点
    + 开放寻址法解决冲突的散列表，删除数据的时候比较麻烦，需要特殊标记已经删除掉的数据。
    + 而且，在开放寻址法中，所有的数据都存储在一个数组中，比起链表法来说，冲突的代价更高。所以，使用开放寻址法解决冲突的散列表，装载因子的上限不能太大。这也导致这种方法比链表法更浪费内存空间。
  + 总结
    + **当数据量比较小、装载因子小的时候，适合采用开放寻址法。这也是 Java 中的`ThreadLocalMap`使用开放寻址法解决散列冲突的原因**

+ 链表法

  + 优点

    + 首先，链表法对内存的利用率比开放寻址法要高。因为链表结点可以在需要的时候再创建，并不需要像开放寻址法那样事先申请好。实际上，这一点也是链表优于数组的地方。
    + 链表法比起开放寻址法，对大装载因子的容忍度更高。开放寻址法只能适用装载因子小于 1 的情况。接近 1 时，就可能会有大量的散列冲突，导致大量的探测、再散列等，性能会下降很多。但是对于链表法来说，只要散列函数的值随机均匀，即便装载因子变成 10，也就是链表的长度变长了而已，虽然查找效率有所下降，但是比起顺序查找还是快很多。

  + 缺点

    + 链表因为要存储指针，所以对于比较小的对象的存储，是比较消耗内存的，还有可能会让内存的消耗翻倍。

    + 而且，因为链表中的结点是零散分布在内存中的，不是连续的，所以对 CPU 缓存是不友好的，这方面对于执行效率也有一定的影响。当然，如果我们存储的是大对象，也就是说要存储的对象的大小远远大于一个指针的大小（4 个字节或者 8 个字节），那链表中指针的内存消耗在大对象面前就可以忽略了。

    + 实际上，对链表法稍加改造，可以实现一个更加高效的散列表。那就是，将链表法中的链表改造为其他高效的动态数据结构，比如跳表、红黑树。这样，即便出现散列冲突，极端情况下，所有的数据都散列到同一个桶内，那最终退化成的散列表的查找时间也只不过是 O(logn)。这样也就有效避免了前面讲到的散列碰撞攻击。

      ![](8.jpg)

  + 总结

    + **基于链表的散列冲突处理方法比较适合存储大对象、大数据量的散列表，而且，比起开放寻址法，它更加灵活，支持更多的优化策略，比如用红黑树代替链表**。





### 工业级散列表举例分析

+ 上述为实现一个工业级散列表需要涉及的一些关键技术，具体的例子，Java 中的 HashMap 这样一个工业级的散列表，来具体看下，这些技术是怎么应用的。

+  初始大小

  + HashMap 默认的初始大小是 16，当然这个默认值是可以设置的，如果事先知道大概的数据量有多大，可以通过修改默认初始大小，减少动态扩容的次数，这样会大大提高 HashMap 的性能。

+ 装载因子和动态扩容

  + 最大装载因子默认是 0.75，当 HashMap 中元素个数超过 0.75*capacity（capacity 表示散列表的容量）的时候，就会启动扩容，每次扩容都会扩容为原来的两倍大小。

+  散列冲突解决方法

  + HashMap 底层采用链表法来解决冲突。即使负载因子和散列函数设计得再合理，也免不了会出现拉链过长的情况，一旦出现拉链过长，则会严重影响 HashMap 的性能。
  + 于是，在 JDK1.8 版本中，为了对 HashMap 做进一步优化，引入了红黑树。而当链表长度太长（默认超过 8）时，链表就转换为红黑树。可以利用红黑树快速增删改查的特点，提高 HashMap 的性能。当红黑树结点个数少于 8 个的时候，又会将红黑树转化为链表。因为在数据量较小的情况下，红黑树要维护平衡，比起链表来，性能上的优势并不明显。

+ 散列函数

  + 散列函数的设计并不复杂，追求的是简单高效、分布均匀。

    ```java
    int hash(Object key) {
        int h = key.hashCode()；
        return (h ^ (h >>> 16)) & (capicity -1); //capicity表示散列表的大小
    }
    ```

    + 其中，hashCode() 返回的是 Java 对象的 hash code。比如 String 类型的对象的 hashCode() 就是下面这样：

      ```java
      public int hashCode() {
        int var1 = this.hash;
        if(var1 == 0 && this.value.length > 0) {
          char[] var2 = this.value;
          for(int var3 = 0; var3 < this.value.length; ++var3) {
            var1 = 31 * var1 + var2[var3];
          }
          this.hash = var1;
        }
        return var1;
      }
      ```

      

### 如何设计的一个工业级的散列函数？

+ 何为一个工业级的散列表？工业级的散列表应该具有哪些特性？结合已经学习过的散列知识，我觉得应该有这样几点要求：
  + 支持快速的查询、插入、删除操作；
  + 内存占用合理，不能浪费过多的内存空间；
  + 性能稳定，极端情况下，散列表的性能也不会退化到无法接受的情况。
+ 如何实现这样一个散列表呢？根据前面讲到的知识，我会从这三个方面来考虑设计思路：
  + 设计一个合适的散列函数；
  + 定义装载因子阈值，并且设计动态扩容策略；
  + 选择合适的散列冲突解决方法。
  + 关于散列函数、装载因子、动态扩容策略，还有散列冲突的解决办法，我们前面都讲过了，具体如何选择，还要结合具体的业务场景、具体的业务数据来具体分析。不过只要朝这三个方向努力，就离设计出工业级的散列表不远了。