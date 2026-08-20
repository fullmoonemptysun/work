![[Pasted image 20260817183653.png]]

```c
  

#include "pico/stdlib.h"

  

#define LED_PIN 25

  

int main(){

  

//init the gpio pin

gpio_init(LED_PIN);

  

//configure the pin as output

gpio_set_dir(LED_PIN, GPIO_OUT);

while(true){

gpio_put(LED_PIN, 1);

sleep_ms(250);

gpio_put(LED_PIN, 0);

sleep_ms(250);

}

  

}
```

- init code, then configuration of the GPIO pin.
- `gpio_put` used to set a pin load to high/low.
- `sleep_ms` : sleep for given milliseconds.

![[Pasted image 20260818022936.png]]