# Python整数对象实现原理

# 定义

整数对象在Python内部用`PyIntObject`​结构体表示: 

```c
typedef struct {
    PyObject_HEAD
    long ob_ival;
} PyIntObject;
```

PyObject\_HEAD宏中定义的两个属性分别是: 

```c
int ob_refcnt;    
struct _typeobject *ob_type;
```

这两个属性是所有Python对象固有的: 

- ob\_refcnt: 对象的引用计数
- ob\_type: 用于描述Python对象的类型信息

‍

小整数对象池small_ints

为优化性能, 缓存[-5, 257)之间整数

```c
#define NSMALLPOSINTS           257
#define NSMALLNEGINTS           5
static PyIntObject *small_ints[NSMALLNEGINTS + NSMALLPOSINTS];
```

‍

大整数缓冲池PyIntBlock

避免不断的malloc分配内存带来的效率损耗

```c
struct _intblock {
    struct _intblock *next;		// 指向下一个PyIntBlock对象
    PyIntObject objects[N_INTOBJECTS]; 	// PyIntObject数组(后转为单链表), 实际存储PyIntObject对象
};
typedef struct _intblock PyIntBlock;

static PyIntBlock *block_list = NULL;

// 所有PyIntBlock内存块中空闲的内存, 存储新PyIntObject对象时, 可快速获取所需内存
static PyIntObject *free_list = NULL; 
```

这些内存块(PyIntBlock)通过一个单向链表组织在一起, 表头是`block_list`​, 表头始终指向最新创建的PyIntBlock对象

‍

创建一个整数对象时, 如果它在小整数范围内, 就直接从小整数缓冲池中直接返回, 如果不在该范围内, 就开辟一个大整数缓冲池内存空间: 

```c
// intobject.c
PyObject* PyInt_FromLong(long ival)
{
     register PyIntObject *v; 

     //[1]尝试使用小整数对象池
     if (-NSMALLNEGINTS <= ival && ival < NSMALLPOSINTS) {
        v = small_ints[ival + NSMALLNEGINTS];
        Py_INCREF(v);
        return (PyObject *) v;
    }

    //[2]为通用整数对象池申请新的内存空间
    if (free_list == NULL) {
        if ((free_list = fill_free_list()) == NULL)
            return NULL;
    }
    //[3](inline)内联PyObject_New的行为
    v = free_list;
    free_list = (PyIntObject *)v->ob_type;
    PyObject_INIT(v, &PyInt_Type);
    v->ob_ival = ival;
    return (PyObject *) v;
}
```

fill_free_list()返回一个`free_list`​链表

当整数对象ival创建成功后, `free_list`​表头就指向了`v->ob_type`​(理解为指向下一个PyIntObject的指针)

```c
// intobject.c
static PyIntObject* fill_free_list(void)
{
    PyIntObject *p, *q;
    // 申请大小为sizeof(PyIntBlock)的内存空间
    // block_list始终指向最新创建的PyIntBlock
    p = (PyIntObject *) PyMem_MALLOC(sizeof(PyIntBlock));
    ((PyIntBlock *)p)->next = block_list;
    block_list = (PyIntBlock *)p;

    // 将PyIntBlock中的PyIntObject数组(objects)转变成单向链表
    p = &((PyIntBlock *)p)->objects[0];
    q = p + N_INTOBJECTS;
    while (--q > p)
        // ob_type指向下一个未被使用的PyIntObject, 
        q->ob_type = (struct _typeobject *)(q-1);
    q->ob_type = NULL;
    return p + N_INTOBJECTS - 1;
}
```

PyIntObject对象销毁时, 它所占用的内存并不会释放, 而是继续被Python使用, 进而将`free_list`​表头指向了这个要被销毁的对象上: 

```c
// intobject.c
static void int_dealloc(PyIntObject *v)
{
    if (PyInt_CheckExact(v)) {
        v->ob_type = (struct _typeobject *)free_list;
        free_list = v;
    }
    else
        v->ob_type->tp_free((PyObject *)v);
}
```
