试了网上的很多方法都不好使，最后还是软链了一下成功了
桌面版的rstudio没办法套用server版本的命令行方法更改设置，只能进rstudio界面后在option里更改。
但是这样难道不会出现环境不合适的问题吗？

```
sudo ln -s /home/erica/anaconda3/envs/r_env/bin/R /usr/bin/R
```
