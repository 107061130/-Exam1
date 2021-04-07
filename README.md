# EXAM1
---
## Test Equipment
![](https://i.imgur.com/SlIBJYd.png)
---
## Result
### Slew rate=1
![](https://i.imgur.com/ncbvG5m.png)
![](https://i.imgur.com/j1EaRif.png)
---

### Slew rate=1/2
![](https://i.imgur.com/6dCx1vG.png)
![](https://i.imgur.com/y3x6VhA.png)
---

### Slew rate=1/4
![](https://i.imgur.com/RWqs29f.png)
![](https://i.imgur.com/6zcr45o.png)
---

### Slew rate=1/8
![](https://i.imgur.com/y7t8L4T.png)
![](https://i.imgur.com/WqsL05r.png)
---

## Code

### Button Select Slew Rate
```
while(1) {
    if (B1 == 1 && f < 1){
        f *= 2;
        f1 /= 2;
    }
    else if (B2 == 1 && f > 0.125){
        f /= 2;
        f1 *= 2;
    }
    else if (B3 == 1){
        break;
    }
    uLCD.color(BLUE);  
    uLCD.background_color(WHITE);
    uLCD.textbackground_color(WHITE);
    uLCD.cls();
    // basic printf demo = 16 by 18 characters on screen
    uLCD.locate(1, 1);
    uLCD.text_width(4); //4X size text
    uLCD.text_height(4);
    uLCD.color(GREEN);
    uLCD.printf("\n1/%d\n", f1); //Default Green on black text
}
```

### Generate Wave and Store ADC_data
```
float a1;
float b = 80;
b = b * f;
a1 = (1.0f  / (sample * (b / 240))) / 0.24;
int k  = 0;

while(1) {  
    // UP  
    for (float i = 0; i < 1; i = i + a1) {
        aout = i;
        if (k >= sample && k < 2*sample)
            ADC[k-sample] = aout;   // store ADC_data
        k++;
        ThisThread::sleep_for(1ms);
    }
    
    // MIDDLE
    for (float i = 0; i < (240 - 2*b); i = i + 1) {
        aout = aout;
        if (k >= sample && k < 2*sample)
            ADC[k-sample] = aout;   // store ADC_data
        k++;
        ThisThread::sleep_for(1ms);
    }
    
    // DOWN
    for (float i = 1; i > 0; i = i - a1) {
        aout = i;
        if (k >= sample && k < 2*sample)
            ADC[k-sample] = aout;   // store ADC_data
        k++;
        ThisThread::sleep_for(1ms);
    }
}
```

## Thread to Output ADC_data

```
t.start(callback(&queue, &EventQueue::dispatch_forever)); // Build an eventqueue for the multiple task 
    sw3.rise(queue.event(Print_ADC));
    
void Print_ADC() {          
    for (int i = 0; i < 1000; i++) {
        printf("%f\r\n", ADC[i]);
        ThisThread::sleep_for(1ms);
    }
}
```
