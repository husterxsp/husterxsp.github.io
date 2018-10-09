---
layout: post
title: java hashcode
date: 2018-09-30
author: "Xsp"
catalog: true
tags:
    - Java
---


如果没有重写 `hashcode`，则是继承自`Object`，是native JNI 方法，实现如下：
http://hg.openjdk.java.net/jdk8/jdk8/hotspot/file/tip/src/share/vm/runtime/synchronizer.cpp

```java
// hashCode() generation :
//
// Possibilities:
// * MD5Digest of {obj,stwRandom}
// * CRC32 of {obj,stwRandom} or any linear-feedback shift register function.
// * A DES- or AES-style SBox[] mechanism
// * One of the Phi-based schemes, such as:
//   2654435761 = 2^32 * Phi (golden ratio)
//   HashCodeValue = ((uintptr_t(obj) >> 3) * 2654435761) ^ GVars.stwRandom ;
// * A variation of Marsaglia's shift-xor RNG scheme.
// * (obj ^ stwRandom) is appealing, but can result
//   in undesirable regularity in the hashCode values of adjacent objects
//   (objects allocated back-to-back, in particular).  This could potentially
//   result in hashtable collisions and reduced hashtable efficiency.
//   There are simple ways to "diffuse" the middle address bits over the
//   generated hashCode values:
//

static inline intptr_t get_next_hash(Thread * Self, oop obj) {
  intptr_t value = 0 ;
  if (hashCode == 0) {
     // This form uses an unguarded global Park-Miller RNG,
     // so it's possible for two threads to race and generate the same RNG.
     // On MP system we'll have lots of RW access to a global, so the
     // mechanism induces lots of coherency traffic.
     value = os::random() ;
  } else
  if (hashCode == 1) {
     // This variation has the property of being stable (idempotent)
     // between STW operations.  This can be useful in some of the 1-0
     // synchronization schemes.
     intptr_t addrBits = cast_from_oop<intptr_t>(obj) >> 3 ;
     value = addrBits ^ (addrBits >> 5) ^ GVars.stwRandom ;
  } else
  if (hashCode == 2) {
     value = 1 ;            // for sensitivity testing
  } else
  if (hashCode == 3) {
     value = ++GVars.hcSequence ;
  } else
  if (hashCode == 4) {
     value = cast_from_oop<intptr_t>(obj) ;
  } else {
     // Marsaglia's xor-shift scheme with thread-specific state
     // This is probably the best overall implementation -- we'll
     // likely make this the default in future releases.
     unsigned t = Self->_hashStateX ;
     t ^= (t << 11) ;
     Self->_hashStateX = Self->_hashStateY ;
     Self->_hashStateY = Self->_hashStateZ ;
     Self->_hashStateZ = Self->_hashStateW ;
     unsigned v = Self->_hashStateW ;
     v = (v ^ (v >> 19)) ^ (t ^ (t >> 8)) ;
     Self->_hashStateW = v ;
     value = v ;
  }

  value &= markOopDesc::hash_mask;
  if (value == 0) value = 0xBAD ;
  assert (value != markOopDesc::no_hash, "invariant") ;
  TEVENT (hashCode: GENERATE) ;
  return value;
}
```


需要注意的是，重写 `equal` 方法的时候需要重写 `hashcode`。
对象 equal 判断相等的时候，hashcode也相等，hashcode相等的时候，equal 不一定相等。

如下代码则输出null。因为 `hashMap.get` 会先判断hashcode。

```java
class People {
    private String name;

    public People(String name) {
        this.name = name;
    }
    @Override
    public boolean equals(Object obj) {
        return this.name.equals(((People) obj).name);
    }
}

public class T {

    public static void main(String[] args) {

        People p1 = new People("hello");
        System.out.println(p1.hashCode());

        HashMap<People, Integer> hashMap = new HashMap<>();
        hashMap.put(p1, 1);

        System.out.println(hashMap.get(new People("hello")));
    }
}
```



参考：

- https://stackoverflow.com/questions/2237720/what-is-an-objects-hash-code-if-hashcode-is-not-overridden/32454673#32454673

- https://stackoverflow.com/questions/15130764/what-is-the-default-implementation-of-hashcode/15130903
- https://www.cnblogs.com/dolphin0520/p/3681042.html