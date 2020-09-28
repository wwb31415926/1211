
#include <stdio.h>
#include<stdlib.h>
#include<math.h>

typedef struct sj
{
    char a[150];//用于存放输入的计算式
    char b[100];//用于存放字母
    int c;//用于存放字母个数
    char d[150];//用于存放后缀表达式
}sj;

int transfer(int x);//将十进制变成二进制
int* fenge(int a, int g);//将二进制分割,只能输入大于等于0的整数
int priority(char op);//求运算符的优先级
int is_operation(char op);//判断是否为运算符
void postfix(struct sj* g);//将一个中缀表达式e转换成为与它等价的后缀表达式
int evalpost(struct sj* g);//后缀表达式的具体运算过程
void scan(struct sj* g);//输入运算式
void jg(struct sj* j, int* b);// 对输入的字符串进行处理

int main()
{
    int  i, b, j,m, * k, result;
   
    sj g,f;
    
    scan(&g);//输入运算式1,挑出字母
    printf("%s    %s   %d\n", g.a, g.b, g.c);
    postfix(&g);//将一个中缀表达式转换成为与它等价的后缀表达式   (a|a|a&c&c)>s#
    printf("%s\n", g.d);
   b = pow(2, g.c);
   for (i = 0; i < g.c; i++)//将字母打印出来 
       printf("%5c", g.b[i]);
   printf("     后缀表达式       result\n");


  for (i = 0; i <b; i++)
  {
      k = NULL;
      j = transfer(i);//将十进制变成二进制，j为二进制
      k = fenge(j,g.c);//将二进制分割,只能输入大于等于0的整数
      for (m = 0; m < g.c; m++)
          printf("%5d", k[m]);
     f = g;
     jg(&f,k);// 对输入的字符串进行处理,即将已经转换成后缀表达式的字符串中的字母换成0、1
     printf("%18s", f.d);
   
     result=evalpost(&f);//后缀表达式的具体运算过程
      printf("     %d\n", result);
     
   }


    return 0;
}
void scan(sj* g)       //输入运算式
{
    int i, j = 0, k, flag = 0;
    printf("请输入计算式（注意：‘！’表示非，‘&’表示合取，‘|’表示析取，‘=’表示等价，'>'示蕴含，最后输入‘#’表示结尾）\n");
     gets(g->a);
    for (i = 0; g->a[i] != '#'; i++)//挑出字母
    {
        if ((g->a[i] >= 65 && g->a[i] <= 90) || (g->a[i] >= 97 && g->a[i] <= 122))
        {
            flag = 0;
            for (k = 0; k < j; k++)
            {
                if (g->b[k] == g->a[i])
                {
                    flag = 1; break;//有重复的字母
                }
            }
            if (flag == 0)//没有重复的字母
            {
                g->b[j] = g->a[i];
                j++;
            }
        }
       
    }
    g->b[j] = '\0';
    g->c = j;//100用于存放字母个数    (!p&q)>!r#   !(p>q)&q&r#     (p&!p)=(q&!q)#
}
void jg(sj* g, int* c)//对输入的字符串进行处理,c是一个整形数组,
{
    int i = 0,k;
    for (i = 0; g->d[i] != '#'; i++)
    {
        if ((g->d[i] >= 'A'&& g->d[i] <= 'Z') || (g->d[i] >= 'a' && g->d[i] <= 'z'))
        {
            for (k = 0; k < g->c; k++)
            {
                if (g->d[i] == g->b[k])
                {
                    if (c[k] == 1)
                        g->d[i] = '1';
                    else
                        g->d[i] = '0';
                    break;
                }
            }

        }
    }
}
                  
int transfer(int x)
{
    int p = 1, y = 0, yushu;
    while (1)
    {
        yushu = x % 2;
        x /= 2;
        y += yushu * p;
        p *= 10;
        if (x < 2)
        {
            y += x * p;
            break;
        }
    }
    return y;
}

int* fenge(int a, int g)//将二进制分割,只能输入大于等于0的整数,a为二进制数，g 为字母个数
{
    int i;
    int* b, c, d = 0, flag = 0;
    b = (int*)malloc(sizeof(int) * g );//通过指针返回分割后的二进制数，第一位放二进制位数
    for (i = 0; i < g; i++)//初始化
        b[i] = 0;
    while (a >= 1)
    {
        c = a % 10;
        a = a / 10;
        b[d] = c;
        d++;
        flag = 1;
    }

    if (flag == 0)//表明输入的数为0
        b[0] = 0;
   
    return b;
}

int priority(char op)//求运算符的优先级
{
    switch (op)
    {
    case '#':return -1;
    case '(':return 0;
    case '!':return 5;
    case '&':return 4;
    case '|':return 3;
    case '>': return 2;
    case'=':return 1;
    default:return -1;
    }
}
int is_operation(char op)
{
    switch (op)
    {
    case'&':case'|':case'!':case'=':case'>':return 1;
    default:return 0;
    }
}

void postfix(sj* g)//将一个中缀表达式转换成为与它等价的后缀表达式
{
    int i = 0, j = 0;
    char opst[100];
    int top, t;
    top = 0;
    opst[top] = '#';
    top++;
    while (g->a[i] != '#')
    {
        if ((g->a[i] >= 65 && g->a[i] <= 90) || (g->a[i] >= 97 && g->a[i] <= 122))//判断是否为字母
            g->d[j++] = g->a[i];
        else if (g->a[i] == '(')
        {
            opst[top] = g->a[i];
            top++;
        }
        else if (g->a[i] == ')')
        {
            t = top - 1;
            while (opst[t] != '(')
            {
                g->d[j++] = opst[--top]; t = top - 1;
            }
            top--;
            if (opst[top - 1] == '!')
            {
                g->d[j++] = '!';
                top--;
            }
        }
        else if (is_operation(g->a[i]))//遇到运算符
        {
           
            if (g->a[i] == '!')
            {
                if (g->a[i + 1] == '0' || g->a[i + 1] == '1')//非在字母前面
                {
                    g->d[j++] = g->a[i + 1]; g->d[j++] = g->a[i];
                    i = i + 2;
                    break;
                }
                else//非在括号前面
                {
                    opst[top] = g->a[i];
                    top++;
                }

            }
            else
            {
                while (priority(opst[top - 1]) >= priority(g->a[i]))//当前运算符的优先级小于栈顶元素时，取出栈顶元素
                                                       //直到栈顶元素的优先级小于当前运算符
                    g->d[j++] = opst[--top];
                opst[top] = g->a[i];
                top++;
            }
        }
        i++;
    }
    while (top)
        g->d[j++] = opst[--top];
    g->d[++j] = '#';
    g->d[++j] = '\0';
}
int evalpost(sj* g)//后缀表达式的具体运算过程
{
    int top = 0;
    int obst[100];//栈
    int i = 0;
    int x1, x2;
    while (g->d[i] != '#')
    {
        if (g->d[i] == '0')
        {
            obst[top++] = 0;
            i++;
        }
        else if (g->d[i] == ' ')
            i++;
        else if (g->d[i] == '1')
        {
            obst[top++] = 1;
            i++;
        }
        else if (g->d[i] == '!')
        {
            if (obst[top-1] == 0)
                obst[top-1] = 1;
            else obst[top-1] = 0;
            i++;
        }
        else if (g->d[i] == '&')
        {
            x1 = obst[--top];
            x2 = obst[--top];
            obst[top] = (x1 && x2);
            top++;
            i++;
        }
        else if (g->d[i] == '|')
        {
            x1 = obst[--top];
            x2 = obst[--top];
            obst[top] = (x1 || x2);
            top++;
            i++;
        }
        else if (g->d[i] == '=')
        {
            x1 = obst[--top];
            x2 = obst[--top];
            obst[top] = (x1 == x2);
            top++;
            i++;
        }
        else if (g->d[i] == '>')
        {
            x1 = obst[--top];
            x2 = obst[--top];
            obst[top] = ((!x2 )|| x1);
            top++;
            i++;
        }
    }
    return obst[0];
}

