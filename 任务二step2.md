* 做题历程
这道题花了我大概有8到9个小时，花费这么多时间的最主要原因就是理解题意理解偏了，定点数存储小数部分32位整数部分32位，本来32的意思只是简单的位数而我却理解成了32个比特位，因为这个中间出现了许许多多的问题（由于表达范围缩小导致溢出，测试一直不通过之类的），花费了大量时间，由于实在感到困惑不能理解所以我询问了出题人，很好的是出题人学长允许我可以就按照原来的思路做不用全部重新改，所以我又在这基础上写后续的代码。在后续做题中减法函数耗时最多，因为我最开始一直用的是网上查的借位的具体思路，写完后测试怎么也不对，后面调试发现借位步骤有问题但我不知道哪里出了问题，思考许久还是决定自己想借位的具体步骤，这中间又花费了许多时间，而且由于全凭自己想象，代码有点史山的感觉，但我已经很尽力优化过了。。。写乘法函数时由于没啥思路所以我是在网上查的，但网上查的跟自己想的总有出入，花费了很久还是不能理解，最后还是自己手写乘法竖式运算，慢慢理解里面的步骤，再把它写到代码里才得以解出来。。写加法操作又是一样的问题，网上的思路我写出来运行有问题，最后又是自己想的思路才做出来。。。。函数写完后我以为我终于解脱了但最后运行时我发现一个致命问题，就是像我这样定义存储的话，小数部分若第一个数是0是无法被存进去的（例如1.01存进去就是1.1），啊发现这个后我都开始神志不清了因为很明显的是这个问题跟最开始的存储方式有关，若要从根本解决我只能全部重新开始。。。。但想了想哎只为了这个重新做不是我做题的初衷，所以我定义了一个全局变量判断是否有这个0，在最后输出的时候将这个0输出出来，但这只是治标不治本呜呜呜呜，，，但除了重新做真的只能这样了。

ps:学长你叫我在溢出的时候自己输入输出样例，所以我在加法操作的时候输入5.22D + 0.89D，输出6.11D， 输入9.22D + 1.9D，输出11.12D
```c

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <math.h>
//该结构体用于表示定点数，包含符号位、整数部分和小数部分
typedef struct{
    int sign;              //符号位，1表示负数，0表示正数
    unsigned int zhengShu;   //32位无符号整数，用于存储定点数的整数部分
    unsigned int xiaoShu;  //32位无符号整数，用于存储定点数的小数部分
}PointFixedNum;
//全局变量用于判断小数部分第一个数是否为零
int zero = 1;

//将二进制转化为十进制的函数(直接引用了任务1写的进制转化函数)
//two是传入的二进制字符串,resultNums是最后计算结果
void twoToTen(char *two, char *resultNums);

//init函数用于初始化PointFixedNum结构体
//num是指向要初始化的PointFixedNum结构体的指针
//input是输入的字符串
void init(PointFixedNum *num, char *input);

//用于去除数据末尾多余的0(直接引用了任务1写的函数)
void removeZero(char *str);

//将输入的字符串中的操作数和符号提取出来
void stringPut(char *input, char *str1, char *str2, char *fuhao);

//加法函数
void add(PointFixedNum *num1,PointFixedNum *num2,PointFixedNum *result);

//将字符串中的数字转化为int类型并判断是否溢出
unsigned int strSwitch(char *str);

//减法函数
void subtruct(PointFixedNum *num1,PointFixedNum *num2,PointFixedNum *result);

//乘法函数
void multiply(PointFixedNum *num1, PointFixedNum *num2, PointFixedNum *result);

int main(){
    //读入输入的字符串
    char string[200];
    fgets(string, 200, stdin);
    //分别存储字符串中的运算数和运算符
    char str1[50],str2[50];
    char fuhao;
    char *p = &fuhao;
    stringPut(string,str1,str2,p);
    //定义结构体分别存储运算数和最终结果
    PointFixedNum num1;
    PointFixedNum num2;
    PointFixedNum result;
    //初始化结构体
    init(&num1,str1);
    init(&num2,str2);
    //根据运算符进行运算
    if(fuhao == '+'){
        add(&num1,&num2,&result);
    }

    if(fuhao == '-'){
        subtruct(&num1,&num2,&result);
    }

    if(fuhao == '*'){
        multiply(&num1,&num2,&result);
    }
    
    //根据正负进行输出
    if(result.sign == 0){
        //根据小数部分第一个是否为零进行输出
        if(zero == 1){
            printf("%u.%uD\n",result.zhengShu,result.xiaoShu);
        }
        else{
            printf("%u.0%uD\n",result.zhengShu,result.xiaoShu);
        }
    }

    if(result.sign == 1){
       if(zero == 1){
            printf("-%u.%uD\n",result.zhengShu,result.xiaoShu);
        }
        else{
            printf("-%u.0%uD\n",result.zhengShu,result.xiaoShu);
        }
    }
    

    return 0;
}
//将输入的字符串中的操作数和符号提取出来
void stringPut(char *input, char *str1, char *str2, char *fuhao){
    int i = 0;
    int temp1 = 0;
    int temp2 = 0;
    int len1 = 0;
    int len2 = 0;
    //遍历字符串提取第一个操作数
    for(i; ;i++){
        if(input[i] == 'D' || input[i] == 'B'){
            str1[len1++] = input[i];
            break;
        }

        str1[len1++] = input[i];
    }
    str1[len1] = '\0';
    //继续遍历字符串提取操作符
    for(i; ;i++){
        if(input[i] == '-' || input[i] == '+' || input[i] == '*'){
            *fuhao = input[i++];
            break;
        }
    }
    //继续遍历提取第二个操作数
    for(i; ;i++){
        if(input[i] == ' '){
            continue;
        }
        if(input[i] == 'D' || input[i] == 'B'){
            str2[len2++] = input[i];
            break;
        }
        str2[len2++] = input[i];
    }
    str2[len2] = '\0';
}

//将二进制转化为十进制的函数
void twoToTen(char *two, char *resultNums) {
    int len = strlen(two);
    int dotPos = -1;
    // 查找小数点位置
    for (int i = 0; i < len; i++) {
        if (two[i] == '.') {
            dotPos = i;
            break;
        }
    }
    double ten = 0;
    if (dotPos == -1) {
        // 如果没有小数点，按照整数转换
        for (int i = 0; len - i - 1 >= 0; i++) {
            if (two[i] == '1') {
                ten += pow(2, len - i - 1);
            }
        }
    } else {
        // 处理整数部分
        for (int i = 0; i < dotPos; i++) {
            if (two[i] == '1') {
                ten += pow(2, dotPos - i - 1);
            }
        }
        // 处理小数部分
        for (int i = dotPos + 1; i < len; i++) {
            if (two[i] == '1') {
                ten += pow(2, dotPos - i);
            }
        }
    }
    //将结果保存在字符串中
    sprintf(resultNums,"%f",ten);

    return;
}

//init函数用于初始化PointFixedNum结构体
//num是指向要初始化的PointFixedNum结构体的指针
//input是输入的字符串
void init(PointFixedNum *num, char *input){
    //使用strdup函数复制输入字符串，以便后续操作
    char *numsStr = strdup(input);
    //查看输入的数字是否为负数将结构体中的sign赋值   
    if(numsStr[0] == '-'){
        num->sign = 1;
        //若为负数将字符串中的负号去掉
        for(int i=0; i<strlen(numsStr) - 1; i++){
            numsStr[i] = numsStr[i+1];
        }
        numsStr[strlen(numsStr)-1] = '\0';
    }else{
        num->sign = 0;
    }
    //如果输入字符串的最后一个字符是'D'，表示输入的是十进制数
    if(input[strlen(input)-1] == 'D'){
        //将复制后的字符串末尾的'D'字符去掉，使其成为一个纯数字字符串
        numsStr[strlen(numsStr)-1] = '\0';
        //在纯数字字符串中查找小数点的位置
        char *dotPos = strchr(numsStr,'.');
        //若数字有小数部分
        if(dotPos){
            //定义两个分别存储小数部分和整数部分的字符串
            char xiaoShuStr[200],zhengShuStr[200];
            //遍历字符串提取整数部分
            int i=0;
            for(; ;i++){
                if(numsStr[i] == '.'){
                    break;
                }
                zhengShuStr[i] = numsStr[i];
            }
            zhengShuStr[i] = '\0';
            
            //利用dotPos指针提取小数部分
            int j=0;
            //若第一位为0
            if(*(dotPos+1) == '0'){
                zero = 0;
            }
            for(; ; j++){
                dotPos++;
                if( *dotPos == '\0' ){
                    break;
                }
                xiaoShuStr[j] = *dotPos;
            }
            xiaoShuStr[j] = '\0';
            
            //利用strSwitch函数将整数和小数部分的字符串转化为int类型并存入结构体           
            num->zhengShu = strSwitch(zhengShuStr);
            num->xiaoShu = strSwitch(xiaoShuStr);
        }
        //若数字没有小数部分
        else{
            num->zhengShu = strSwitch(numsStr);
            num->xiaoShu = 0;
        }
    }
    //如果输入字符串的最后一个字符是'B'，表示输入的是二进制数
    if(input[strlen(input)-1] == 'B'){
        //将复制后的字符串末尾的'B'字符去掉，使其成为一个纯数字字符串
        numsStr[strlen(numsStr)-1] = '\0';
        //用于存储二进制转化为十进制后的结果
        char resultNums[200];
        //二进制转化为十进制
        twoToTen(numsStr, resultNums);
        //去掉转化后末尾多出的0
        removeZero(resultNums);

//由于已将二进制转化为十进制存储在字符串中后续操作同上面的存储操作
        char *dotPos = strchr(numsStr,'.');
        //若数字有小数部分
        if(dotPos){
            //定义两个分别存储小数部分和整数部分的字符串
            char xiaoShuStr[200],zhengShuStr[200];
            //遍历字符串提取整数部分
            int i=0;
            for(; ;i++){
                if(numsStr[i] == '.'){
                    break;
                }
                zhengShuStr[i] = numsStr[i];
            }
            zhengShuStr[i] = '\0';
            
            //利用dotPos指针提取小数部分
            int j=0;
            for(; ; j++){
                dotPos++;
                if( *dotPos == '\0' ){
                    break;
                }
                xiaoShuStr[j] = *dotPos;
            }
            xiaoShuStr[j] = '\0';
            
            //利用strSwitch函数将整数和小数部分的字符串转化为int类型并存入结构体           
            num->zhengShu = strSwitch(zhengShuStr);
            num->xiaoShu = strSwitch(xiaoShuStr);
        }
        //若数字没有小数部分
        else{
            num->zhengShu = strSwitch(numsStr);
            num->xiaoShu = 0;
        }
    }
    free(numsStr);
}

//用于去除数据末尾多余的0(直接引用了任务1写的函数)
void removeZero(char *str) {
    // 计算字符串长度
    int len = strlen(str);
    // 当字符串长度大于1且最后一个字符是0时
    while (len > 1 && str[len - 1] == '0') {
        // 将最后一个字符置为空字符（即删除该字符）
        str[len - 1] = '\0';
        // 字符串长度减1
        len--;
    }
    // 如果最后一个字符是小数点
    if (str[len - 1] == '.') {
        // 将最后一个字符置为空字符（即删除该字符）
        str[len - 1] = '\0';
    }
}

//将字符串中的数字转化为int类型并判断是否溢出
unsigned int strSwitch(char *str) {
    unsigned int num = 0;
    int i = 0;
    while (str[i]!= '\0') {
        //判断溢出，对于无符号整数，只要大于UINT_MAX / 10，或者等于UINT_MAX / 10且下一位大于UINT_MAX % 10就溢出
        //该种判断方法由查阅得出的
        if (num > UINT_MAX / 10 || (num == UINT_MAX / 10 && (str[i] - '0') > UINT_MAX % 10)) {
            printf("溢出发生\n");
            return UINT_MAX;
        }
        num = num * 10+(str[i] - '0');
        i++;
    }
    return num;
}
//加法函数
void add(PointFixedNum *num1,PointFixedNum *num2,PointFixedNum *result){
    //记录第一个加数小数部分的位数
    unsigned int temp = num1->xiaoShu;
    int cnt1 = 0;
    while (temp > 0){
        temp /= 10;
        cnt1++;
    }
    //记录第二个加数小数部分的位数
    temp = num2->xiaoShu;   
    int cnt2 = 0;
    while (temp > 0){
        temp /= 10;
        cnt2++;
    }
    int cntMax = cnt1>cnt2?cnt1:cnt2; 
    unsigned long long tempXiao;
    //小数部分相加（其中要考虑高位对高位所以有条件判断谁的位数高）
    if(cnt1 > cnt2){
        tempXiao = (unsigned long long)num1->xiaoShu + (unsigned long long)num2->xiaoShu*pow(10,cnt1-cnt2);
    }
    else{
        tempXiao = (unsigned long long)num2->xiaoShu + (unsigned long long)num1->xiaoShu*pow(10,cnt1-cnt2);
    }
    //判断溢出
    if(tempXiao > pow(2,32)-1){
        printf("发生溢出");
    }
    //如果小数部分有进位
    int jinWei = 0;
    if(tempXiao >= pow(10,cntMax)){
        tempXiao -= pow(10,cntMax);
        jinWei = 1;
    }
    //将计算结果存入结构体中
    result->zhengShu = num1->zhengShu + num2->zhengShu +jinWei;
    result->xiaoShu = (unsigned int)tempXiao;

    return;
}
//减法函数
void subtruct(PointFixedNum *num1,PointFixedNum *num2,PointFixedNum *result){
    int jieWei = 0;
    //若两数同号作减法
    if(num1->sign == num2->sign){    
        //临时存储小数部分计算结果
        unsigned int temp1;   
        //若被减数大于等于减数 
        if(num1->zhengShu >= num2->zhengShu){
        //当被减数的小数部分小于减数的小数部分时需要借位
            if(num1->xiaoShu < num2->xiaoShu){
                //计算小数部分有几位以便后续借位处理
                unsigned int temp = num2->xiaoShu;
                int cnt = 0;
                while (temp > 0){
                    temp /= 10;
                    cnt++;
                }
                //算出小数部分最终结果（如减数的小数部分为2被减数小数部分为1，就等于pow(10,1)-2+1)
                temp1 = pow(10,cnt) - num2->xiaoShu + num1->xiaoShu;
                //整数部分减去借位的值
                jieWei = 1;
            }
            //当被减数的小数部分不小于减数的小数部分时不需要借位
            else{
                temp1 = num1->xiaoShu - num2->xiaoShu;
            }
            //将小数部分的运算结果存入结构体
            result->xiaoShu = temp1;
            //将整数部分的运算结果存入结构体
                result->zhengShu = num1->zhengShu - num2->zhengShu - jieWei;
                result->sign = 0;                 
        }
        //若被减数小于减数（相当于将交换减数和被减数）
        if(num1->zhengShu < num2->zhengShu){
            //当被减数的小数部分小于减数的小数部分时需要借位
            if(num2->xiaoShu < num1->xiaoShu){
                //计算小数部分有几位以便后续借位处理
                unsigned int temp = num1->xiaoShu;
                int cnt = 0;
                while (temp > 0){
                    temp /= 10;
                    cnt++;
                }
                //算出小数部分最终结果（如减数的小数部分为2被减数小数部分为1，就等于pow(10,1)-2+1)
                temp1 = pow(10,cnt) - num1->xiaoShu + num2->xiaoShu;
                //整数部分减去借位的值
                jieWei = 1;
            }
            //当被减数的小数部分不小于减数的小数部分时不需要借位
            else{
                temp1 = num2->xiaoShu-num1->xiaoShu;
            }
            //将小数部分的运算结果存入结构体
            result->xiaoShu = temp1;

            result->zhengShu = num2->zhengShu - num1->zhengShu - jieWei;
            result->sign = 1;
        }            
    }
    //若两数异号相当于加法
    if(num1->sign != num2->sign){
        //若第一个数为正数第二个数为负数
        if(num1->sign == 0){
            add(num1,num2,result);
            return;
        }
        //若第一个数为负数第二个数为正数
        if(num1->sign == 1){
            add(num1,num2,result);
            result->sign = 1;
            return;
        }
    }
    return;
}

//第一个数为被乘数第二个数为乘数
void multiply(PointFixedNum *num1, PointFixedNum *num2, PointFixedNum *result){
    //先存入计算结果的正负
    result->sign = ((num1->sign == num2->sign)?0:1);
    //将被乘数的整数部分和小数部分合并为一个数方便后续乘法运算
    unsigned int temp = num1->xiaoShu;
    //记录被乘数小数点后有几位
    int cnt1 = 0;
    while (temp > 0){
        temp /= 10;
        cnt1++;
    }
    //合并的同时去掉小数点方便后续运算
    unsigned int long long temp1 = (unsigned int long long)(num1->zhengShu * pow(10,cnt1) + num1->xiaoShu);
    //将乘数的整数部分和小数部分合并为一个数方便后续乘法运算
    temp = num2->xiaoShu;
    //记录乘数小数点后有几位
    int cnt2 = 0;
    while (temp > 0){
        temp /= 10;
        cnt2++;
    }
    if( zero == 0 ){
        cnt2++;
    }
    //合并的同时去掉小数点方便后续运算
    unsigned int long long temp2 = (unsigned int long long)(num2->zhengShu * pow(10,cnt2) + num2->xiaoShu);
    //存储计算结果
    unsigned int long long tempResult = 0;
    //用于记录下面乘法运算循环的次数用于移位
    int count = 0;

    while(temp2 != 0){
        //记录乘数的最后一位数字使其与被乘数相乘
        int lastWei = temp2 % 10;
        //记录后将乘数最后一位数字去掉
        temp2 /= 10;
        //核心运算（乘数最后一位乘以被乘数，再移位补零加上原来的结果）
        tempResult += (lastWei * temp1) * pow(10,count);
        count++;
    }        
    //溢出判断
    if(tempResult > pow(2,32)-1){
        printf("发生溢出");
        return;
    }
    //利用乘数和被乘数小数部分位数之和在合适的地方加上小数点
    //并将整数部分和小数本分存入结构体中
    result->zhengShu = (unsigned int)(tempResult / pow(10,cnt1+cnt2));
    result->xiaoShu = (unsigned int)(tempResult % (unsigned int long long)pow(10,cnt1+cnt2));

    return;
}