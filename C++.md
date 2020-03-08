# 1.Fri Mar  6 09:00:16 CST 2020钢条切割朴素法

* 期望：
  
* 给定价格表，求长度为4的切割方案，期望结果为10.
  
* 错误：

  * 返回20.

* 源码：

  ```c++
  int  simpleRe(int *p,int n)
  {
      if(n==0) return 0;
      int q=-1; 
      for(int i=1;i<=n;i++)
          q=max(q,p[i]+simpleRe(p,n-i));
      return q;
  }
  int p[10]={1,5,8,9,10,17,17,20,24,30};
      cout<<simpleRe(p,4)<<endl;
  ```

* 解决：

  * 在simpleRe中加断点疯狂调试，一开始不认真，后来认真看发现q的返回值不对劲。于是乎突然明白了，函数中想法是i从1到n,p[i]表示i寸的钢条的价格。但是实际的价格表数组下标从0开始。

* 改正：

  * ```C++
     for(int i=1;i<=n;i++)
            q=max(q,p[i-1]+simpleRe(p,n-i));
    ```

* 反思：

  * 真的还是那句话，你想要的不一定就是你想要的,实际的p[i]和你想象中的p[i]不同。有点不了解给的数据的形式了。数组0~n-1.

* 类型：

  * 变量意义不一致。

# 2.Fri Mar  6 18:20:30 CST 2020弱智显示矩阵

* 期望：长度16数组，期望控制台显示一个4*4矩阵形式

* 错误：
  
  * `0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 请按任意键继续. . .`
  
* 源码：

  * ```C++
     for(int i=0;i<16;++i)
        {
            if(i!=0&&!i%4) cout<<endl;
            cout<<m[i]<<" ";
        }
    ```

* 解决：

  * 反反复复看，怎么看逻辑都是对的i%4表示对4取余，逢4,8,12,16会换行，为什么没有换。后来打点也看不出什么，于是int n=i%4,这样就可以调试的时候单独看i%4得值了，然后瞬间懂了。尼玛币，！优先级太高。

* 改正：

  * `        if(i!=0&&!(i%4)) cout<<endl;`        

* 反思：

  * 着急了，犯这种低级错误，小心。

* 类型：

  * 运算符优先级搞错

# 3.Fri Mar  6 21:20:01 CST 2020LCS朴素递归

* 期望：

  * ABCBDAB和BDCABA返回最长长度4.

* 错误：

  * 返回3.

* 源码：

  * ```C++
    int lcsSimpleRe(string a,string b,int i,int j)
    {
        if(i==0||j==0) return 0;
        else if(a[i]==b[j]) return lcsSimpleRe(a,b,i-1,j-1)+1;
        else return max(lcsSimpleRe(a,b,i-1,j),lcsSimpleRe(a,b,i,j-1));
    }
    string a="ABCBDAB";
    string b="BDCABA";
    cout<<lcsSimpleRe(a,b,a.size()-1,b.size()-1);
    ```

* 解决：

  * 首先反复看，感觉这个递归逻辑毫无问题。然后画图还是没问题。于是缩小规模试一试，令a="A",b="B",结果竟然返回0.看了一下第九句，恍然大悟。a.size()-1=b.size()-1=0,直接返回0了。

* 改正：

  * ```C++
    int lcsSimpleRe(string a,string b,int i,int j)
    {
        if(i==-1||j==-1) return 0;
        else if(a[i]==b[j]) return lcsSimpleRe(a,b,i-1,j-1)+1;
        else return max(lcsSimpleRe(a,b,i-1,j),lcsSimpleRe(a,b,i,j-1));
    }
    ```

* 反思：

  * 跟1错误一模一样，逻辑上一般习惯从一开始到n，逻辑上这样思考没问题，当i或j等于0是意味着某一个串为空了，但是具体代码实现的时候要记得数组是0~n-1.代码逻辑毫无问题，就是忘了数组这茬了。

* 错误类型：

  * 变量意义不一致

# 4.Sat Mar  7 08:29:42 CST 2020LCS动态规划

* 期望：

  * ```
    4
    4
     -1   0   0   0  -1  -1  -1
      0   0   0   0   1  -1  -1
     -1   1   1   1   1  -1  -1
      0   1   1   2   2  -1  -1
     -1   1   1   2   2   3  -1
     -1  -1   2   2   2   3  -1
     -1  -1  -1  -1   3  -1   4
     -1  -1  -1  -1  -1   4   4 请按任意键继续. . .
    ```

* 错误：

  * ```
    4
    2
     -1   0   0   0   0  -1  -1
      0   0   0   0   0  -1  -1
      0   0   0   0   0  -1  -1
     -1   1   1   1   1   1  -1
      0   1   1   1   1   1  -1
      0   1   1   1   1   1  -1
     -1   1   2   2   2   2   2
      0   1   2   2   2   2   2 请按任意键继续. . .
    ```

* 源码：

  * ```C++
    int dpTopDownReExtend(string a,string b,int i,int j,int *m,int *s)//自顶向下的给出最优解的动态规划
    {
       int q=*(m+i*(b.size()+1)+j);
       if(q>=0) return q;
       if(i==0||j==0) *(m+i*(b.size()+1)+j)=0;
       else if(a[i-1]==b[i-1]) *(m+i*(b.size()+1)+j)=dpTopDownReExtend(a,b,i-1,j-1,m,s)+1;
       else *(m+i*(b.size()+1)+j)=max(dpTopDownReExtend(a,b,i-1,j,m,s),dpTopDownReExtend(a,b,i,j-1,m,s));
       return *(m+i*(b.size()+1)+j);
    }
    void Lcs(string a,string b)
    {
        int *m=new int[(a.size()+1)*(b.size()+1)];
        for(int i=0;i<(a.size()+1)*(b.size()+1);++i)
            m[i]=-1;
        int *s=new int[a.size()*b.size()];
        for(int i=0;i<a.size()*b.size();++i)
            s[i]=0;
        cout<<dpTopDownReExtend(a,b,a.size(),b.size(),m,s)<<endl;
        for(int i=0;i<(a.size()+1)*(b.size()+1);++i)
        {
            if(i!=0&&!(i%(b.size()+1))) cout<<endl;
            cout<<setw(3)<<m[i]<<" ";
        }
    }
     string a="ABCBDAB";
        string b="BDCABA";
        // string a="A";
        // string b="AB";
        cout<<lcsSimpleRe(a,b,a.size()-1,b.size()-1)<<endl;
        Lcs(a,b);
    ```

* 解决：

  * 当时已经晚上了，刚吃完药，脑子有点昏昏沉沉，眼睛也快看不清了，扫了几遍代码，觉得毫无问题。LCS这个函数，坑定没问题，然后就一直看递归的函数，怎么看怎么没毛病，就是朴素递归加了备忘而已，于是乎试小规模的a,b还是不行，于是手推程序，没有任何问题，但结果就是不对。后来终于发现了第六行是两个i，心态崩了。

* 改正：

  * ```C++
       int q=*(m+i*(b.size()+1)+j);
       if(q>=0) return q;
       if(i==0||j==0) *(m+i*(b.size()+1)+j)=0;
       else if(a[i-1]==b[j-1]) *(m+i*(b.size()+1)+j)=dpTopDownReExtend(a,b,i-1,j-1,m,s)+1;
       else *(m+i*(b.size()+1)+j)=max(dpTopDownReExtend(a,b,i-1,j,m,s),dpTopDownReExtend(a,b,i,j-1,m,s));
       return *(m+i*(b.size()+1)+j);
    ```

* 反思：

  * 果然是首先犯了错，别着急的马上断点调试，手撸代码，先认认真真看一遍代码。

* 类型：

  * 打字错误

# 5.Sat Mar  7 14:50:14 CST 2020数组循环移位

* 期望：

  * ```
    0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
    6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 0 1 2 3 4 5
    请按任意键继续. . .
    ```

    

* 错误：

  * ```
    0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
    6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 1 2 3 4 5 6
    请按任意键继续. . .
    ```

* 源码：

  * ```C++
    void shift(int *a,int n,int k) //笨比循环,O(k*n)+O(1)
    {
        for(int i=0;i<k;i++ )
        {
            int temp=0;
            for(int j=0;j<n-1;j++)
            {
                temp=a[0];
                a[j]=a[j+1]; 
            }
            a[n-1]=temp;
        }
    }
        shift(a,21,6); 
    ```

* 解决：

  * 这么简单的代码，怎么看都毫无问题，为啥不对，0去哪儿了？断点，手撸都没问题。后来通过缩小规模，shift(a,21,1)才发现第8行，你以为的temp不是你以为的temp，你以为他保存了移动前的a[0]其实他在循环内执行了n次，最终的值是原来的a[1],现在的a[0]，而不是原来的a[0]，刚才的手撸代码什么的，都默认temp值是对的了，其实不对。

* 改正：

  * ```C++
    void shift(int *a,int n,int k) //笨比循环,O(k*n)+O(1)
    {
        for(int i=0;i<k;i++ )
        {
            int temp=a[0];
            for(int j=0;j<n-1;j++)
                a[j]=a[j+1]; 
            a[n-1]=temp;
        }
    }
    ```

* 反思：

  * 检查错误的时候不要想当然的认为某些变量的值就是你要的值，不要忽略去检查它，在用某些值的时候一定仔细看那句涉及那个变量的值改变的额语句，看是否这个变量经过一系列修改赋值之后最后的结果真的是你要的值

* 类型：

  * 变量意义不一致

# 6.Sun Mar  8 15:54:44 CST 2020归并排序归并

* 期望：

  * ```
    20 21 20 19 18 17 16 15 14 13 12 11 1 2 3 4 5 6 7 8 9
    1 2 3 4 5 6 7 8 9 11 12 13 14 15 16 17 18 19 20 20 21
    请按任意键继续. . .
    ```

* 错误：

  * ```
    20 21 20 19 18 17 16 15 14 13 12 11 1 2 3 4 5 6 7 8 9
    9 13 13 13 13 13 18 18 18 18 18 18 18 18 18 -1414812757 20 -1414812757 -1414812757 -1163005939 -1163005939
    请按任意键继续. . .
    ```

* 源码：

  * ```C++
    void vector::merge(int lo,int mi,int hi) //二路归并
    {
        int a=mi-lo,b=hi-mi;int c=lo;
        int *m=new int[a];
        for(int i=0;i<a;i++)
            m[i]=_elem[i];   //前半段并不一定从0开始哦，所以m[i]=_elem[i]纯属脑瘫
        int i=0,j=mi;
        while(a>0||b>0)
        {
            if(!b||(m[i]<=_elem[j])) { _elem[lo++]=m[i++];a--;} //别忘了等号，很关键，而且等号加在前半段，让前半段的相同元素出现在前面,保证排序稳定性
            if(!a||(_elem[j]<m[i])) {_elem[lo++]=_elem[j++];b--;}
        }
        delete []m;m=nullptr;//记得别内存泄漏
    }
    ```

* 解决：

  * 第四句的错误，你是脑瘫吗，复制原数组的前半部分都搞不明白，lo能是1吗？10,11两句的经典错误，二路归并的时候例如两个数组a，b，当b为0或者a首元素大于b首元素的时候，归位。如果前半部分判断失败，即b不为0，那么此时别急着比较啊，a可能为0，，，

* 修改：

  * ```C++
    void vector::merge(int lo,int mi,int hi)
    {
        int as=mi-lo,bs=hi-mi;
        int loo=lo;
        int *a=new int[as];
        for(int i=0;i<as;i++)
            a[i]=_elem[loo++];
        int i=0,j=mi;
        while(as>0||bs>0)
        {
            if(!bs||(as>0&&a[i]<=_elem[j])){_elem[lo++]=a[i++];as--;}
            if(!as||(bs>0&&_elem[j]<a[i])){_elem[lo++]=_elem[j++];bs--;}
        }
        delete []a;a=nullptr;//凡是动态申请内存，一定注意别内存泄漏
    }
    
    ```

* 反思：

  * 别死记硬背代码，要记逻辑与思路

* 类型：

  * 数组下标越界

# 7.Sun Mar  8 20:46:59 CST 2020插入排序

* 期望：

  * 20 21 20 19 18 17 16 15 14 13 12 11 1 2 3 4 5 6 7 8 9
    1 2 3 4 5 6 7 8 9 11 12 13 14 15 16 17 18 19 20 20 21
    请按任意键继续. . .

* 错误：

  * 20 21 20 19 18 17 16 15 14 13 12 11 1 2 3 4 5 6 7 8 9
    20 20 19 18 17 16 15 14 13 12 11 1 2 3 4 5 6 7 8 9 21
    请按任意键继续. . .

* 源码：

  * ```C++
    void vector::insertSort(int lo,int hi)
    {
        for(int i=lo+1;i<hi;i++)
        {
            int insertPoint=i;
            for(int j=lo;j<i;j++)
                if(_elem[j]>_elem[i]) insertPoint=j; //查找插入点，可以调用search O(logn)
            if(insertPoint==i) continue;
            else 
            {
                int temp=_elem[i];
                for(int k=i;k>insertPoint;k--)   //插入O(n)
                    _elem[k]=_elem[k-1];
                _elem[insertPoint]=temp;
            }
        }
    }
    ```

* 解决：

  * 先缩小待排序数组的规模，然后仔细看错误。无果。然后定点。没认真看，无果，感觉没什么问题。然后输出insertPoint的值，发现问题，值不对，然后继续定点，终于发现问题了。在6,7行，本意是找到第一个大于待插入数的值，然后insertPoint记录其下标方便之后插入。但是这个循环找到insertPoint就应该退出，但是循环还会执行，insertPoint不再是第一个大于待插入数的位置

* 改正：

  * ```C++
    void vector::insertSort(int lo,int hi)
    {
        for(int i=lo+1;i<hi;i++)
        {
            int insertPoint=i;
            for(int j=lo;j<i;j++)
                if(_elem[j]>_elem[i]) 
                {
                    insertPoint=j; //查找插入点，可以调用search O(logn),一旦找到就结束循环
                    break;
                }
            if(insertPoint==i) continue;
            else 
            {
                int temp=_elem[i];
                for(int k=i;k>insertPoint;k--)   //插入O(n)
                    _elem[k]=_elem[k-1];
                _elem[insertPoint]=temp;
            }
        }
    }
    ```
  
* 反思：

  * 小心谨慎吧，一找到就应该退出循环的额，大意了。
  
* 类型：
  
  * 变量意义不一致
  
  





