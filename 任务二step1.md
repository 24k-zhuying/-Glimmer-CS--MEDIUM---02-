* 做题历程
这道题花时一个多小时，感觉只要思路捋清了很快就能做出来。为了方便我选择先将二进制转化为十进制再将其存储在结构体中，代码中直接引用了前面写的两个个函数方便进制转化
```c
/*了解结构体的内存布局，怎么才能让我们定义的结构体占据最小的内存？
答：将结构体中的成员按照类型大小从小到大的顺序排列。原因较小类型的成员先排列可以减少因对齐而产生的填充字节*/


#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>

//该结构体用于表示定点数，包含符号位、整数部分和小数部分
typedef struct{
    int sign;              //符号位，1表示负数，0表示正数
    unsigned int zhengShu;   //32位无符号整数，用于存储定点数的整数部分
    unsigned int xiaoShu;  //32位无符号整数，用于存储定点数的小数部分
}PointFixedNum;

//将二进制转化为十进制的函数(直接引用了任务1写的进制转化函数)
//two是传入的二进制字符串,resultNums是最后计算结果
void twoToTen(char *two, char *resultNums);

//init函数用于初始化PointFixedNum结构体
//num是指向要初始化的PointFixedNum结构体的指针
//input是输入的字符串
void init(PointFixedNum *num, char *input);

//用于去除数据末尾多余的0(直接引用了任务1写的函数)
void removeZero(char *str);
//将字符串中的数字转化为int类型并判断是否溢出
unsigned int strSwitch(char *str);

int main(){
    //定义结构体num
    PointFixedNum num;
    //输入字符串
    char input[100];
    scanf("%s", input);
    //将字符串的信息存入结构体
    init(&num, input);

    //输出结果
    if(num.sign == 1){
        printf("-%u.%uD",num.zhengShu,num.xiaoShu);
    }
    else{
        printf("%u.%uD",num.zhengShu,num.xiaoShu);
    }
    
    return 0;
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
            for(int i=0; ;i++){
                if(numsStr[i] == '.'){
                    break;
                }
                zhengShuStr[i] = numsStr[i];
            }
            //利用dotPos指针提取小数部分
            for(int j=0; ; j++){
                dotPos++;
                if( *dotPos == '\0' ){
                    break;
                }
                xiaoShuStr[j] = *dotPos;
            }
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
        char *dotPos = strchr(resultNums,'.');
        //若数字有小数部分
        if(dotPos){
            //定义两个分别存储小数部分和整数部分的字符串
            char xiaoShuStr[200],zhengShuStr[200];
            //遍历字符串提取整数部分
            for(int i=0; ;i++){
                if(resultNums[i] == '.'){
                    break;
                }
                zhengShuStr[i] = resultNums[i];
            }
            //利用dotPos指针提取小数部分
            for(int j=0; ; j++){
                dotPos++;
                if( *dotPos == '\0' ){
                    break;
                }
                xiaoShuStr[j] = *dotPos;
            }
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
        // 判断溢出，对于无符号整数，只要大于UINT_MAX / 10，或者等于UINT_MAX / 10且下一位大于UINT_MAX % 10就溢出
        if (num > UINT_MAX / 10 || (num == UINT_MAX / 10 && (str[i] - '0') > UINT_MAX % 10)) {
            printf("溢出发生\n");
            return UINT_MAX;
        }
        num = num * 10+(str[i] - '0');
        i++;
    }
    return num;
}