1. 对Arrays.asList()的集合进行add/remove/clear操作为什么有问题？

   Arrays.asList()返回的集合是Arrays的内部类ArrayList，此类继承了AbstractList，但没有重写add/remove/clear方法，而AbstractList的这几个方法内部实现都是直接抛异常，不允许直接调用