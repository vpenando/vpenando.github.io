## Work in progress

Our `Component` class:
```cs
class Component {
  // ...
}
```

The base component:
```cs
class RootComponent<T> : Component {
  private readonly List<T> obj;
 
  public delegate bool UnaryPred<U>(U param);
 
  public RootComponent() {
    this.obj = new List<T>();
  }
 
  public void Add<U>(U val) where U : class, T
    => this.obj.Add(val);
    
  public U Get<U>(UnaryPred<U> pred) where U : class, T
    => obj.FirstOrDefault(o => o is U && pred(o as U)) as U;
    
  public bool Contains<U>() where U : class, T
    => this.Get<U>(o => true) != null;
}
```
