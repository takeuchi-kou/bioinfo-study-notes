参考：https://blog.csdn.net/kuyu05/article/details/106186936

- 对于我来说有用的步骤是：  

修改R目录下的`\etc\Rprofile.site`文件，在尾部增加`Sys.setenv(LANG = "en_US")`



- 其他可进行的操作：

1. 到R目录下的`\etc`的`Rconsole`文件，修改`language = en`

2. 在R studio的console里输入`Sys.setenv(LANG = "en_US")`。这种操作应该和直接改profile文件是一样的。

