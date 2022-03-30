# LEVEL 3 - cherry-pick

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
