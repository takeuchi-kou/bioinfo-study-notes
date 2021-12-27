# Boyer- Moore Basic

## 1. from right to left

与exact naive 方法一样，我们依然是把P串从T串的最左边向右边移动，但是在比对环节，则是从右向左比对

![image-20211225162431417](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225162431417.png)

## 2. Bad character rule

比对时遇到mismatch，就跳过这个，直到(a)遇到一个match, (b)移动整个P串到T串mismatch字符的右边

### step 1(情况a)

从右到左比对，直到遇到一个mismatch；

继续在P串上向左找，直到找到一个与mismatch的T串字符相同的字符；

把那个相同字符移动到T串mismatch的位置。

### step 2（情况b）

依然是从右到左比对，直到遇到一个mismatch；

在P串上找不到与当前mismatch的T串字符相同的字符；

这时移动整个P串到T串mismatch字符的右边（因为没有可以匹配上的字符存在）

![image-20211225163611342](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225163611342.png)

## 3. Good suffix rule

从右到左比较，匹配上的字符串称为t，遇到一个mismatch之后，继续向P串左边找，直到(a)找到一串与t相同的字符串，(b)移动整个P串到t串mismatch字符的右边

"*感觉上像是条件更宽松的KMP*"

### step 1 (a)

从右到左比对，直到遇到一个mismatch，而在mismatch之前所有match上的字符串称为t；

继续在P串上向左找，直到找到一个与t的完全相同的字符串；

把那个相同字符串移动到t的位置。

### step 2 (b)

同样是从右到左比对，直到遇到一个mismatch，而在mismatch之前所有match上的字符串称为t；

继续在P串上向左找，直到找到一个与t的后缀完全相同的前缀；

把那个与t串后缀相同的P串前缀移动到t的后缀位置。

![image-20211225170332390](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225170332390.png)

## 4. 总结两条rule

Bad character是在P串上寻找能让mismatch变为match的位置；

Good suffix 是在P串上寻找能让t串继续保持match的位置。

# Boyer- Moore altogether

综合运用两条rule，看哪条能skip更多字符，就用哪条

## 1. A Example

### step 1

P串最右边的字符是mismatch的，所以无法运用good suffix rule，只能按照bad character rule 在P串左边找下一个match的字符

![image-20211225170935066](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225170935066.png)

### step 2

这一步里，bad character rule无法 shift，因为mismatch的左边紧跟着一个match，幸好可以使用good suffix

![image-20211225171133782](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225171133782.png)

### step 3

这一步，在P串右边既不能找到bad char 也不能找到 good suffix，然而按照good suffix的shift规则能够跳过更多字符，所以按照good suffix进行shift。

![image-20211225171317569](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225171317569.png)

![image-20211225171348707](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225171348707.png)

## 2. preprocessing

与KMP算法相同，Boyer- Moore算法也需要提前制作look up table，这个table同样也是完全使用P串进行制作。依靠这个table，我们可以直接判断将P串shift的长度。

而不一样的是，Boyer- Moore需要为两个rule分别制作一个table，取shift更多的那个。

![image-20211225171911082](https://gitee.com/joy_thestraydog/typora/raw/master/img/image-20211225171911082.png)