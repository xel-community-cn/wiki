<!-- TITLE: ePl Simple Job -->

<!-- SUBTITLE: A quick summary of ePl Simple Job -->

# ePL的简单任务

```java
   1000;

function verify {
    u[0]=m[0];
    u[1]=m[1];
    verify_bty (u[0]<1000);
    verify_pow (u[0],u[1],u[2],u[3]);
}

function main {
    verify();
}
```