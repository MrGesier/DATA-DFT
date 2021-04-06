# DATA-DFT
Financial Data filters

//@version=3
study("Low Frequency Fourier Transform", overlay = true, shorttitle="MR_Gesier_FFTRes")

//This Study uses the Real Discrete Fourier Transform algorithm to generate 3 sinusoids possibly indicative of future price. 
//I got information about this RDFT algorithm from "http://www.dspguide.com/ch8/5.htm" and "http://www.dspguide.com/ch8/6.htm".

//Declaration of const
pi = acos(-1)

//Declaration of user-defined variables
N = input(defval=64, title="Sample Time Window", type=integer, minval=2, maxval=600, confirm=false, step=1, options=[2,4,8,16,32,50,64,100,128,200,256,512,1024,1200,2048,4096])
price=avg(open,close)
vol=volume
//Real part of the Frequency Domain Representation
ReX(k) =>
    sum = 0.0
    for i=0 to N-1
        sum := sum + close[i]*cos(2*pi*k*i/N)
    return = sum
    
//Imaginary part of the Frequency Domain Representation
ImX(k) =>
    sum = 0.0
    for i=0 to N-1
        sum := sum + close[i]*sin(2*pi*k*i/N)
    return = -sum

//Get sinusoidal amplitude from frequency domain  
ReX_(k) =>
    case = 0.0
    if(k!=0 and k!=N/2)
        case := 2*ReX(k)/N
    if(k==0)
        case := ReX(k)/N
    if(k==N/2)
        case := ReX(k)/N
    return = case
    
 //Get sinusoidal amplitude from frequency domain  
ImX_(k) =>
    return = -2*ImX(k)/N
    
//Get full Fourier Transform
x(i, N) =>
    sum1 = 0.0
    sum2 = 0.0
    for k=0 to N/2
        sum1 := sum1 + ReX_(k)*cos(2*pi*k*i/N) 
    for k=0 to N/2
        sum2 := sum2 + ImX_(k)*sin(2*pi*k*i/N)
    return = sum1+sum2
    
//Get single constituent sinusoid on price
sx(i, k, N) =>
    sum1 = ReX_(k)*cos(2*pi*k*i/N)
    sum2 = ImX_(k)*sin(2*pi*k*i/N)
    return = sum1+sum2
    
//risk distance
risklenght=close - sx(0,0,64)/sx(0,0,64)*100
plot(risklenght)


//Plot 3 Low-Freq Sinusoids
plot(sx(0,0,N), color=#ffffff)
plot(sx(0,1,N), color=#006600)
plot(sx(0,2,N), color=#cc3333)
plot(sx(0,3,N), color=#ff0000)
plot(sx(0,0.5,N),color=#3366ff)
plot(sx(0,0,N)+(sx(0,1,N)+sx(0,2,N)+sx(0,3,N)), color=#FFFF00)

//Declaration constante
line1=sx(0,0,N)+sx(0,1,N)+sx(0,2,N)+sx(0,3,N)
line2=sx(0,0,N)
p1 = plot(line1)
p2 = plot(line2)
fill(p1, p2, color = line1 > line2 ? #00CC00 : #ffffff)

//32
line3=sx(0,0,32)+sx(0,1,32)+sx(0,2,32)+sx(0,3,32)
line4=sx(0,0,32)
p3 = plot(line3)
p4 = plot(line4)
fill(p3, p4, color = line3 > line4 ? #00CC00 : #ffffff)

//128
line5=sx(0,0,128)+sx(0,1,128)+sx(0,2,128)+sx(0,3,128)
line6=sx(0,0,128)
p5 = plot(line5)
p6 = plot(line6)
fill(p5, p6, color = line5 > line6 ? #00CC00 : #ffffff)

//256
line7=sx(0,0,256)+(sx(0,1,256)+sx(0,2,256)+sx(0,3,256))
line8=sx(0,0,256)
p7 = plot(line7)
p8 = plot(line8)
fill(p7, p8, color = line7 > line8 ? #00CC00 : #ffffff)

//512
line9=sx(0,0,512)+(sx(0,1,512)+sx(0,2,512)+sx(0,3,512))
line10=sx(0,0,512)
p9 = plot(line9)
p10 = plot(line10)
fill(p9, p10, color = line9 > line10 ? #00CC00 : #ffffff)

//1024
//line11=sx(0,0,1024)+(sx(0,1,1024)+sx(0,2,1024)+sx(0,3,1024))
//line12=sx(0,0,1024)
//p11 = plot(line11)
//p12 = plot(line12)
//fill(p11, p12, color = line11 > line12 ? #00CC00 : #ffffff)

line13=sx(0,0,16)+sx(0,1,16)+sx(0,2,16)+sx(0,3,16)
line14=sx(0,0,16)
p13 = plot(line13)
p14 = plot(line14)
fill(p13, p14, color = line13 > line14 ? #00CC00 : #ffffff)

//8
line15=sx(0,0,8)+sx(0,1,8)+sx(0,2,8)+sx(0,3,8)
line16=sx(0,0,8)
p15 = plot(line15)
p16 = plot(line16)
fill(p15, p16, color = line15 > line16 ? #00CC00 : #ffffff)
