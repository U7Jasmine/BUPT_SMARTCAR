# 北京邮电大学智能工程与自动化小学期循迹小车

------

Purastar Edit on 2024 Sep.1th

> [!NOTE]
>
> 这里是邮茉莉的传承小屋，整理了祖传及新生代码（），后文会给出代码解释和修改内容

## 测试代码

```c
#include<reg51.h>
#include <intrins.h>

#define uchar unsigned char  // 定义uchar为unsigned char
#define uint unsigned int  // 定义uint为unsigned int
#define light_on 0  // 定义light_on为0
#define light_off 1  // 定义light_off为1
#define B 1  // 定义B为1（黑色）
#define W 0  // 定义W为0（白色）

unsigned short int j = 0;  // 声明短整数j，初始化为0

sbit IN1 = P1 ^ 3;  // IN1连接到P1.3
sbit IN2 = P1 ^ 5;  // IN2连接到P1.5
sbit IN3 = P1 ^ 2;  // IN3连接到P1.2
sbit IN4 = P1 ^ 0;  // IN4连接到P1.0

void r1() {
    IN1 = 0;
    IN2 = 1;
}

void r0() {
    IN1 = 0;
    IN2 = 0;
}

void l1() {
    IN3 = 1;
    IN4 = 0;
}

void l0() {
    IN3 = 0;
    IN4 = 0;
}

void l2() {
    IN3 = 0;
    IN4 = 1;
}

void r2() {
    IN1 = 1;
    IN2 = 0;
}

void forward() {
    int t = 900;
    for (j = 0; j < t; j++) {
        l1();
        r1();
    }
    for (j = 0; j < 1000 - t; j++) {
        l0();
        r0();
    }
}

void main()
{
    while(1)
        forward();
}

```

> [!CAUTION]
>
> 下载到小车上后小车会向前跑，如果并非此现象请将不前转的电机两接线端线互换，当然你也可以通过修改代码中的引脚定义，
>
> ```c
> sbit IN1 = P1 ^ 3;
> sbit IN2 = P1 ^ 5;
> sbit IN3 = P1 ^ 2;
> sbit IN4 = P1 ^ 0;//改变"P1^"后的数字即可
> ```
>
> 完成以上的操作你就可以完美的食（ji）用（cheng）前辈的代码了。



## 以下是循迹代码



### PWM的体现

~~~c(前进代码)
for (j = 0; j < 900; j++)  //前进用PWM是因为消除左右轮的差速故0110为双轮前进(没有可以不用)
	{
		IN1 = 0;  //右轮前为01
		IN2 = 1;  
		IN3 = 1;  //左轮前为10
		IN4 = 0;  
	}
	for (j = 0; j < 100; j++) 
	{
		IN1 = 0;  
		IN2 = 0;  
		IN3 = 1;  
		IN4 = 0;  
	}
~~~

==为了防止代码过于雷同，笔者在此删除了LED 以及蜂鸣器的功能以便消除共性，所以需要读者自行编写（狗头保命，不写也不影响循迹功能。==

```c
//以下提供三种代码，只有代码一为笔者编写，二三笔者太懒了直接粘下来了
#include<reg51.h>
#include <intrins.h>

#define uchar unsigned char
#define uint unsigned int
#define B 1
#define W 0

unsigned short int j = 0;

sbit IN1 = P1 ^ 3;
sbit IN2 = P1 ^ 5;
sbit IN3 = P1 ^ 2;
sbit IN4 = P1 ^ 0;

sbit infrared1 = P3 ^ 7;
sbit infrared2 = P3 ^ 6;
sbit infrared3 = P3 ^ 5;
sbit infrared4 = P3 ^ 4;
sbit infrared5 = P3 ^ 3;
void r1() {
    IN1 = 0;
    IN2 = 1;
}

void r0() {
    IN1 = 0;
    IN2 = 0;
}

void l1() {
    IN3 = 1;
    IN4 = 0;
}

void l0() {
    IN3 = 0;
    IN4 = 0;
}

void l2() {
    IN3 = 0;
    IN4 = 1;
}

void r2() {
    IN1 = 1;
    IN2 = 0;
}

    void forward() {
        int t = 900;//t为直线时的速度，上限为1000，调节此处。
        for (j = 0; j < t; j++) {
            l1();
            r1();
        }
        for (j = 0; j < 1000 - t; j++) {
            l0();
            r0();
        }
    }

void low_left() {
    for (j = 0; j < 100; j++) {
        r1();
        l0();
    }
    for (j = 0; j < 300; j++) {
        l1();
        r1();
    }
}

void low_right() {
    for (j = 0; j < 100; j++) {
        r0();
        l1();
    }
    for (j = 0; j < 300; j++) {
        r1();
        l1();
    }
}

void turn_left() {
    for (j = 0; j < 500; j++) {
        l0();
        r0();
    }
    for (j = 0; j < 90; j++) {
        l1();
        r1();
    }
}

void turn_right() {
    for (j = 0; j < 500; j++) {
        r0();
        l1();
    }
    for (j = 0; j < 90; j++) {
        r1();
        l1();
    }
}

void slow_left() {
    for (j = 0; j < 90; j++) {
        l0();
        r1();
    }
    for (j = 0; j < 500; j++) {
        l1();
        r1();
    }
}

void slow_right() {
    for (j = 0; j < 90; j++) {
        r0();
        l1();
    }
    for (j = 0; j < 500; j++) {
        r1();
        l1();
    }
}

void sharp_left() {
    for (j = 0; j < 1000; j++) {
        l2();
        r1();
    }
}

void sharp_right() {
    for (j = 0; j < 1000; j++) {
        l1();
        r2();
    }
}

void turn_back() {
    for (j = 0; j < 100; j++) {
        l1();
        r2(); 
    }
}

void main() {
    P0 = 0xff;
    P2 = 0xff;
    P1 = 0xff;
    while (1)
    {
        if (infrared3 == W & infrared1 == W & infrared2 == W & infrared4 == W & infrared5 == W) {
            turn_back();
        } else if (infrared3 == B & infrared1 == B & infrared2 == B & infrared4 == B & infrared5 == B) {
            forward();
        } else if (infrared3 == B & infrared1 == W & infrared2 == B & infrared4 == B & infrared5 == W) {
            forward();
        } else if (infrared3 == W & infrared1 == B & infrared2 == B & infrared4 == W & infrared5 == W) {
            turn_left();
        } else if (infrared3 == W & infrared1 == W & infrared2 == W & infrared4 == B & infrared5 == B) {
            turn_right();
        } else if (infrared3 == B & infrared1 == B & infrared2 == B & infrared4 == W & infrared5 == W) {
            sharp_left();
        } else if (infrared3 == B & infrared1 == W & infrared2 == W & infrared4 == B & infrared5 == B) {
            sharp_right();
        } else if (infrared3 == W & infrared1 == B & infrared2 == W & infrared4 == W & infrared5 == W) {
            sharp_left();
        } else if (infrared3 == W & infrared1 == W & infrared2 == W & infrared4 == W & infrared5 == B) {
            sharp_right();
        } else if (infrared3 == W & infrared1 == W & infrared2 == B & infrared4 == W & infrared5 == W) {
            low_left();
        } else if (infrared3 == W & infrared1 == W & infrared2 == W & infrared4 == B & infrared5 == W) {
            low_right();
        } else if (infrared3 == B & infrared1 == W & infrared2 == B & infrared4 == W & infrared5 == W) {
            slow_left();
        } else if (infrared3 == B & infrared1 == W & infrared2 == W & infrared4 == B & infrared5 == W) {
            slow_right();
        } else if (infrared3 == B & infrared1 == B & infrared2 == W & infrared4 == W & infrared5 == W) {
            sharp_left();
        } else if (infrared3 == B & infrared1 == W & infrared2 == W & infrared4 == W & infrared5 == B) {
            sharp_right();
        } else if (infrared3 == B & infrared2 == W & infrared1 == W & infrared4 == W & infrared5 == W) {
            forward();
        } else if (infrared3 == B & infrared1 == B & infrared2 == B & infrared4 == B & infrared5 == W) {
            sharp_left();
        } else if (infrared3 == B & infrared1 == W & infrared2 == B & infrared4 == B & infrared5 == B) {
            sharp_right();
        } else {
            forward();
        }
    }
}
```

当然，cv大概率是不能正常运行的（除非你运气好跟笔者接线一模一样），需要根据你的接线修改开头定义的引脚。

------



```c
//方案二
#include<reg52.h>
#include <intrins.h> 
#define uchar unsigned char
#define uint unsigned int
#define t 1
sbit IN1=P1^3;
sbit IN2=P1^5;
sbit IN3=P1^2;
sbit IN4=P1^0;

sbit infrared1 = P3^2;
sbit infrared2 = P3^6;
sbit infrared3 = P3^5;
sbit infrared4 = P3^4;
sbit infrared5 = P3^3;					
					  
void Delay_ms(uint xms)      	//延时程序，xms是形式参数
{  																										            
	uint i, j;
	for(i=xms;i>0;i--)          	 // i=xms,即延时xms, xms由实际参数传入一个值
		for(j=115;j>0;j--);		     //此处分号不可少（循环体）
}

void stop()
{
	IN1=0; 
	IN2=0;
	IN3=0;
	IN4=0;
}	
void go_ahead()
{
    IN1=0; 
	IN2=1;
	IN3=1;
	IN4=0;
}	
void left(){
	IN1 = 0;
	IN2 = 1;
	IN3 = 0;
	IN4 = 1;
}
void right()
{
	IN1 = 1;
	IN2 = 0;
	IN3 = 1;
	IN4 = 0;
}     

void main()
{  
	int L, R;
    P0=0xff;
    P2=0xff;
    P1=0xff;
   while(1)
   {   
stop();
	  Delay_ms(6);		
   	  R = 0,L = 0;
	   if(infrared1 ) L= L+1;
	   if(infrared5 ) R= R+1;
if(infrared2 ) L=L+1;
	   if( infrared4 )	R= R+1;
	   if(R > L){
	   		right();
			Delay_ms(2);		
	   }
 else if( L > R ){
	   		left();
			LED1=1，LED2=0;				
Delay_ms(2);				
}
	   else if(infrared3==1 && R==L){
			go_ahead();
			Delay_ms(6);
	   }
	   else {	
	   		right();
	   		Delay_ms(4);
	   }
	}	
}
```



------

```c
//方案三，
#include<reg51.h>
#include <intrins.h>
#define uchar unsigned char
#define uint unsigned int
#define t 1
uchar code seg_data[ ] = {0xc0,0xf9,0xa4,0xb0,0x99,0x92,0x82,0xf8,0x80,0x90,0xff};
uchar data disp_buf[7] = {0x00,0x00,0x00,0x00,0x00,0x00,0x00};

sbit IN1=P1^3;
sbit IN2=P1^5;
sbit IN3=P1^2;
sbit IN4=P1^0;
sbit LED1=P2^6;
sbit LED2=P2^7;	
sbit S1=P1^6;
sbit left2 = P3^3;
sbit left1 = P3^4;
sbit middle = P3^5;
sbit right1 = P3^6;
sbit right2 = P3^7;
unsigned int count=0;					  
 
void Delay_ms(uint xms)      	
{  																										            
	uint i, j;
	for(i=xms;i>0;i--)          	 
		for(j=115;j>0;j--);		     
}
void Delay_mms(uint xms)      	
{  																										            
	uint i, j;
	for(i=xms;i>0;i--)          	 
		for(j=11;j>0;j--);		     
}

display()
{
   P0=seg_data[0];  				
   P2=0xfe;				            
   Delay_ms(t);						
   P0=seg_data[1];  				
   P2=0xfd;							
   Delay_ms(t);						
   P0=seg_data[2];  				
   P2=0xfb;				            
   Delay_ms(t);						
   P0=seg_data[3];  				
   P2=0xf7;							
   Delay_ms(t);						
   P0=seg_data[4];  				
   P2=0xef;				            
   Delay_ms(t);						
   P0=seg_data[5];  				
   P2=0xdf;							
   Delay_ms(t);						
   P2=0xff;
}

stop()
{
	IN1=0; 
	IN2=0;
	IN3=0;
	IN4=0;
}
qianjin()
{
	IN1=0;
	IN2=1;
	IN3=1;
	IN4=0;
}
left()
{
    IN2=1;
	IN3=0;
	Delay_mms(1);		   
	IN4=1;
	Delay_mms(9);		   
}
right()
{
    IN2=0;
	IN3=1;
	Delay_mms(1);
	IN1=1;
	Delay_mms(9);
}		
turn_left()
{
    IN1=0;
	IN2=1;
	IN3=0;
	IN4=0; 
}
turn_right()
{
    IN1=0;
	IN2=0;
	IN3=1;
	IN4=0;
}
turn_left2()
{
    IN1=0;
	IN2=1;
	IN3=0;
	IN4=1;
}	
turn_right2()
{
    IN1=1;
	IN2=0;
	IN3=1;
	IN4=0;
}
     
void main()
{  
    P0=0xff;
    P2=0xff;
    P1=0xff;
	P3=0xff;
    count = 0;
    while(1)
    {
	    if((middle==1&&right2==0&&left2==0&&left1==0&&right1==0)||(left2==1&&left1==0&&middle==1&&right1==0&&right2==0)||(left2==0&&left1==0&&middle==1&&right1==0&&right2==1)||(left2==1&&left1==0&&middle==1&&right1==1&&right2==1)||(left2==1&&left1==1&&middle==1&&right1==0&&right2==1))
		{
		    qianjin();
			Delay_ms(2);
		}
		else if(left2==0&&left1==1&&middle==1&&right1==1&&right2==1)
		{
		    turn_right();
			Delay_ms(1);
		}
		else if(left2==1&&left1==1&&middle==1&&right1==1&&right2==0)
		{
		    turn_left();
			Delay_ms(1);
		}
		else
		{
		    if(left2==1&&left1==1&&middle==1&&right1==1&&right2==1)
			{
			    qianjin();
				Delay_ms(2);
			}
		    else if(left1==1)
			{
			    if(left2==1)
				{
				    turn_left2();
					Delay_ms(1);
				}
				else
				{
			        left();
					IN2=1;
					IN4=1;
				}
			}
			else if(right1==1)
			{
			    if(right2==1)
				{
				    turn_right2();
					Delay_ms(1);
				}
				else
				{
			        right();
					IN2=1;
					IN4=1;
				}
			}
			else if(middle==0)
			{
			    if(right2==1)
				{
				    turn_right2();
					Delay_ms(1);
				}
				else if(left2==1)
				{
				    turn_left2();
					Delay_ms(1);
				}
				else if(left1==0&&left2==0&&right1==0&&right2==0)
		        {
		            turn_right2();
		    	    Delay_ms(1);
		        }
			}
		}
		stop();
		Delay_ms(1);
    }
}
```

------

本文由markdown编写，本着继承的原则，希望师弟师妹们对本文进行改进完善，本文参考[北京邮电大学小学期代码](https://github.com/YasenWang/BUPT-Smart-Car)

本文已放置github以便师弟师妹更新。以下是项目链接[电子电工综合实训小车](https://github.com/U7Jasmine/BUPT_SMARTCAR)
