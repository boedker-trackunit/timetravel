# LEVEL 3 - cherry-pick

Taking commits from one branch - and adding them to another.


![](/Back_to_future-deloran-dmc-time_machine-2x-terabass.jpg)

*<div style="text-align:right">-- Photo by Oto Godfrey - [Creative Commons Attribution 3.0 Unported license](https://creativecommons.org/licenses/by/3.0/deed.en)</div>*


**Typical scenario**
If a critical bug is fixed in another branch - and you want it before it is available in master.


```
master      ---(A)--->(B)--->(C)--->(D)
                         \             \
> feature                 \             (K)-->(L)-->(M)
                           \
feature2                    (X)-->(Y)-->(Z)
```

Scenario: stealing or borrowing a commit from another branch 

```
$ git cherry-pick HASH_OF_Y
```


```
master      ---(A)--->(B)--->(C)--->(D)
                         \             \
> feature                 \             (K)-->(L)-->(M)-->(Y*)
                           \
feature2                    (X)-->(Y)-->(Z)
```
