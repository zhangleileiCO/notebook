### vue监听数据变化

- data 变化触发界面刷新的实现
- data 变化触发计算属性计算
- data 变化触发$watcher逻辑执行

#### Observer

initMixin -> initState -> initData
**initData:**

- 遍历data中的所有属性添加访问代理，使this.[propertyName]可以直接访问
- 创建Oberver对象

```javascript
  constructor (value: any) {
    this.value = value
    this.dep = new Dep()
    this.vmCount = 0
    //给每一个vue实例对象定义一个私有__ob__属性
    def(value, '__ob__', this)
    if (Array.isArray(value)) {
      if (hasProto) {
        protoAugment(value, arrayMethods)
      } else {
        copyAugment(value, arrayMethods, arrayKeys)
      }
      this.observeArray(value)
    } else {
      //遍历data的属性调用defineReactive方法
      this.walk(value)
    }
  }
/**
 * Define a reactive property on an Object.
 */
export function defineReactive (
  obj: Object,
  key: string,
  val: any,
  customSetter?: ?Function,
  shallow?: boolean
) {
  // 每个属性 都有一个dep方法，这里形成了一个闭包，每次调用data属性的get set 方法会访问这个属性
  //Onserver本身也有一个dep属性，目前还不清楚用途
  //dep和属性一一对应
  const dep = new Dep()

  const property = Object.getOwnPropertyDescriptor(obj, key)
  if (property && property.configurable === false) {
    return
  }

  // cater for pre-defined getter/setters
  const getter = property && property.get
  const setter = property && property.set
  if ((!getter || setter) && arguments.length === 2) {
    val = obj[key]
  }

  let childOb = !shallow && observe(val)
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      const value = getter ? getter.call(obj) : val
      // Dep Function对象有一个target属性,Function对象属性不会附加到原型对象Function.proptype)上，那它是以什么角色存在呢？
      if (Dep.target) {
        // 再每次获取data属性的值时（也就是调用get方法），会调用当前属性dep对象的depend方法
        //这个方法的功能是，把当前Dep.target加入到dep的subs中
        dep.depend()
        if (childOb) {
          childOb.dep.depend()
          if (Array.isArray(value)) {
            dependArray(value)
          }
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      const value = getter ? getter.call(obj) : val
      /* eslint-disable no-self-compare */
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return
      }
      /* eslint-enable no-self-compare */
      if (process.env.NODE_ENV !== 'production' && customSetter) {
        customSetter()
      }
      // #7981: for accessor properties without setter
      if (getter && !setter) return
      if (setter) {
        setter.call(obj, newVal)
      } else {
        val = newVal
      }
      childOb = !shallow && observe(newVal)
      dep.notify()
    }
  })
}
```

### Watcher

上面只有Observer、Dep和将watcher加入到dep.subs中的逻辑，缺少watcher实例对象初始化的部分
watcher实例对象是在哪里初始化的呢？
Dep.target是在哪里赋值的呢？

- ###### lifecycle.js中mountComponent方法
  
  *这里是对render方法加入的wacher监听*
  
  每次Observer的监听的属性的get方法触发，就调用Dep的notify方法
  
  notify遍历监听者属性变化的所有wacther,调用wacher的update方法
  
  watcher的update方法执行属性变化之后的所有操作，在这个case中是调用updateComponent方法
  
  updateComponent方法调用Component的render方法
  
- 二

- 三