# Elevador

![Arduino_Logo_Registered svg](https://user-images.githubusercontent.com/109388659/234407445-1de9faf7-fd9b-4d31-9f8d-089b83dd0892.png)
## Integrantes

-Ivan Gonzalez

## Parcial practico de SPD
![image](https://github.com/Ivan-Ex-Gonzalez/Elevador/assets/109388659/200bc7c0-87e7-4df9-bbba-b85215d1cf0a)

## Descripcion

sistema que puede recibir ordenes de subir, bajar o pausar desde diferentes pisos y muestre el estado actual del montacargas en el display 7 segmentos.

##Entradas digitales

![image](https://github.com/Ivan-Ex-Gonzalez/Elevador/assets/109388659/178cac20-5e05-4823-b012-0b8c083a8a40)

-3 Pulsadores configurados en PULLUP

##Salidas digitales

![image](https://github.com/Ivan-Ex-Gonzalez/Elevador/assets/109388659/1e419046-4bca-4800-90ae-3e6cdcf1bf60)

-2 Leds con una resistencia de 220 ohms en cada uno.
-1 Display de 7 segmentos con una resistencia de 300 en cada segmento

## funcion principal
-Todas las definiciones al principio del codigo:
```c++
#define A 11
#define B 12
#define C 6
#define D 7
#define E 8
#define F 10
#define G 9
#define led_v 5
#define led_r 4
#define subir A0
#define pausar A1
#define bajar A5
```
-La variable listanumeros[] es una matriz de 8 filas que contiena los segmetos que le corresponden a cada numero del 0 al 9 y es utilizada para 
darle a la funcion Controlador_de_7_segmentos() la lista que del piso correspondiente para subir o bajar de piso.

```c++
int listanumeros[][8] = {
    			 {A, B, C, D, E, F, -1},
                         {B, C, -1},
                         {A, B, G, D, E, -1},
                         {A, B, C, D, G, -1},
  			 {B, F, C, G, -1},
  			 {A, F, C, D, G, -1},
  			 {A, F, C, D, E, G, -1},
                         {A, B, C, -1},
                         {A, B, C, D, E, F, G, -1},
                         {A, B, G, D, F, C, -1}};
```


-La funcion ascensor() recibe por parametro los digitalread bajo, subo y pauso. Esta funcion se repite de forma indefinada y dependiendo de que pulsador de presione
llamara a la funcion Bajar_o_subir() o pausa().
```c++
void ascensor(int subir, int bajar, int pausar){
    if (subir == LOW && piso < 9) {
      Bajar_o_subir(1);
    }
  	if (bajar == LOW && piso > 0) {
      Bajar_o_subir(0);
    }
  	if (pausar == LOW){
      	antirrebote = 1;
    }
    if (pausar == HIGH && antirrebote == 1){
        antirrebote = 0;
        digitalWrite(led_r, HIGH);
    	pausa();
        digitalWrite(led_r, LOW);
      
    }
}
```
-La funcion Bajar_o_subir() recibe por parametro un entero, si este entero es 1 la funcion detectara que debe subir 1 piso mientras que si no es 1 lo interpretara 
como que debe bajar. en ambos casos la funcion controla el led verde y llama a la funcion espera() y a Controlador_de_7_segmentos().
```c++
void Bajar_o_subir(int mover){
	digitalWrite(led_v, HIGH);
    espera();
    Controlador_de_7_segmentos(listanumeros[piso], LOW);
  	if (mover == 1){
  		piso++;
  	}
  	else{
  		piso--;
  	}
    digitalWrite(led_v, LOW);
    Serial.println(piso);
    Controlador_de_7_segmentos(listanumeros[piso], HIGH);
}
```
-La funcion Controlador_de_7_segmentos() recibe por parametro una lista y un int teniendo el valor de HIGH O LOW, la funcion se encarga de recorrer 
esa lista y encender o apagar los segmentos asignados en esa lista dependiendo del int.
```c++
void Controlador_de_7_segmentos(int lista[],int estado){
  for (int i = 0; lista[i] != -1; i++){
  	int letra = lista[i];
    digitalWrite(letra, estado);
  }
}
```
-La funcion espera() se encarga de ejecutar un dilay de 100 30 veces y si en mitad de eso se preciona el pulsador pausar, llama a la funcion pausa().
```c++
void espera(){
  for (int i = 0; i <= 30; i++){
    int pauso = digitalRead(pausar);
    delay(100);
    if (pauso == LOW){
      	antirrebote = 1;
    }
    if (pauso == HIGH && antirrebote == 1){
        antirrebote = 0;
        digitalWrite(led_v, LOW);
    	pausa();
        digitalWrite(led_v, HIGH);
    }
  }
}
```
-La funcion pausa() se encarga de mantener actuvo un bucle indefinido hasta que se presione el pulsador pausar.
```c++
void pausa() {
  int bandera = 1;
  digitalWrite(led_r, HIGH);
  while (bandera == 1) {
    int pauso = digitalRead(pausar);
    if (pauso == LOW) {
      antirrebote = 1;
    }
    if (pauso == HIGH && antirrebote == 1){
      antirrebote = 0;
      bandera = 0;
      digitalWrite(led_r, LOW);
    }
  }
}
```

## :eight_pointed_black_star:Link al proyecto

[Proyecto] https://www.tinkercad.com/things/fbSTyVxgdlR
