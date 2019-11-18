# 结构

```scala
type Vector3 record {
    def x mut int;
    def y mut int;
    def z mut int;
}
def v = Vector3@{
    x = 1,
    y = 2,
    z = 3
}
```