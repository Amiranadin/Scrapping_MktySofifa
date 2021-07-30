# Scrapping_MktySofifa
El siguiente repositorio, consiste en la extracción de información de la seleccion chilena de futbol vs la seleccion chilena mostrada en el juego FIFA. Esta informacion extraida por dos paginas web. Con el objetivo de identificar si el juego muestra exactamente la realidad de los jugadores.


En los siguientes sitios web extraeremos la información necesaria para la investigación acerca de los jugadores de la seleccion chilena.


La primera pagina web, muestra informacion acerca de los jugadores en la vida real. 
https://www.transfermarkt.es/chile/kader/verein/3700/saison_id/2020

La segunda pagina web, muestra informacion de los jugadores en el juego de play mas conocido como FIFA.

https://sofifa.com/team/111459/chile/?hl=es-ES


Para iniciar la investigacion, primero se debe instalar las siguientes paqueterias, para poder utilizar ciertos comandos.


Commands:

library("rvest")
install.packages("readxl")
install.packages("dplyr")

library(readxl)
library(dplyr)



Luego, el primer paso es realizar la extracion de informacion de la primera pagina web llamada: Transfermarkt. Es decir, se designa por nombre chimkt y se utiliza el comando real_html para poder leer la pagina web.

### Primera pagina web ###

Abiendo pagina web:

chimkt <- read_html("https://www.transfermarkt.es/chile/kader/verein/3700/saison_id/2020")

A continuacion, se utiliza el print para imprimir y verificar si efectivamente nos muestra la pagina web.


print(html_text(chimkt))


En segundo lugar, se comienza a navegar en la pagina web, con el objetivo de extraer informacion de los jugadores en la vida real. Aqui encontramos el nombre del futbolista, edad y valor de mercado

chimkt <- read_html("https://www.transfermarkt.es/chile/kader/verein/3700/saison_id/2020")

Para extraer la informacion de la tabla de la pagina web, se realiza con el class "items" debido a que la pagina web, no posee atributo id. Se utilizan los comandos html_table para la extraccion directa  y html_nodes para extraccion con nodos

TABLAjugadores <- html_nodes(chimkt,css = ".items")


MKTjugadores <- html_table(TABLAjugadores)[[1]]

print(html_text(TABLAjugadores[1]))


La tabla de jugadores extraida en el punto anterior, es una data sucia debido a que los nombre de los encabezados se extrajeron desordenados, por lo que asignamos siguientes nombres a los encabezados con el fin de ordenarlos y limpiarlo.


Con estos comandos, asignamos con nombre, posicion, edad, valor de mercado a las columnas que aportarian a la investigacion y a las colummnas que necesitamos eliminar les asignamos un numero, para asi posteriormente poder eliminar la colummna, ya que su contenido estaba sucio y en formato nulo.

names(MKTjugadores)= c("DORSAL", "2", "3", "NOMBRE", "POSICION", "EDAD", "7", "VALOR DE MERCADO")

Posterior a la asignacion de nombres, eliminamos las columnas 2,3,7 para que finalmente nos quedaran solamente las columnas que nos sirven


Comandos para eliminar columnas 2.3 y 7.

MKTjugadores["2"] = NULL
MKTjugadores["3"] = NULL
MKTjugadores["7"] = NULL

MKTjugadoresLIMPIO <- na.omit(MKTjugadores)

Para realizar el analisis de los jugadores, evidenciamos que el valor de mercado de la tabla esta sucia debido a que es un formato texto y numerico y Necesitamos que sea en formato numerico, para un analisis mas especifico.


Ahora asignamos el formato final de la tabla


Con los siguientes comandos:

EDADJUGADORMKT = MKTjugadoresLIMPIO$EDAD

NOMBREJUGADORMKT = MKTjugadoresLIMPIO$NOMBRE

VALORMERCADOMKT = c("3800000","1000000","450000","12000000","6000000","2000000","1500000","500000","1800000","950000","9000000","4000000","3100000","1500000","1000000","6000000","3500000","800000","1400000","900000","5000000","850000","2000000","3000000","7500000","7000000","4000000","600000","25000")

VALORMERCADOMKTNUMERICO <- as.numeric(VALORMERCADOMKT)

MKTDATAFINAL = data.frame(EDADJUGADORMKT,NOMBREJUGADORMKT,VALORMERCADOMKTNUMERICO)


Para confirmar si la data esta el formato correcto y en el que necesitamos, visualizamos la tabla MKTDATA FINAL CON el comando view mostrado a continuacion:

View(MKTDATAFINAL)

Finalizada la extraccion y la limpieza de la primera pagina web, se procede a la segunda fase, que consiste en la extraccion de informacion de la segunda pagina web
.

### Segunda pagina web ###

Se procede a extraer la informacion de la segunda pagina web, con la asignacion chisof y el comando read_html para permitir que el programa leea la pagina en el comando.

chisof <- read_html("https://sofifa.com/team/111459/chile/?hl=es-ES")

Se utiliza el comando print, para que el programa nos imprima la informacion  de la pagina web.


print(html_text(chisof))

Para extraer la informacion se utiliza la class "list", debido a que al igual que nuestra primera pagina, no poseen ID. y se utilizan los comandos html_nodes y html_table


TABLAjugadoresSOF <- html_nodes(chisof,css = ".list")


SOFjugadoresLIMPIO <- html_table(TABLAjugadoresSOF)[[1]]



print(html_text(TABLAjugadoresSOF[[1]]))


Se procede a limpiar la data. Es decir las columnas debido a que la data tablajudaoressof es una data sucia, que no servira para el objetivo final

Con los siguiente comandos se elimminas las columnas:

SOFjugadoresLIMPIO["X1"] = NULL
SOFjugadoresLIMPIO["X4"] = NULL
SOFjugadoresLIMPIO["X5"] = NULL
SOFjugadoresLIMPIO["X8"] = NULL
SOFjugadoresLIMPIO["X9"] = NULL
SOFjugadoresLIMPIO["X10"] = NULL

names(SOFjugadoresLIMPIO)= c("NOMBRE", "EDAD", "POSICION", "VALOR DE MERCADO")


Data final limpia debido a que el precio de valor de mercado estaba en formato umerico y texto. Se repite procedimiento a la primera pagina.


EDADJUGADORSOF = SOFjugadoresLIMPIO$EDAD

NOMBREJUGADORSOF = SOFjugadoresLIMPIO$NOMBRE

VALORMERCADOSOF = c("1200,000","4100000","2700000","2800000","10500000","16000000","6000000","18000000","6000000","18000000","3100000","2100000","4300000","2800000","0","0","1900000","7000000","0","475000","450000","36000000","1100000")

as.numeric(VALORMERCADOSOF)

VALORMERCADOSOFNUMERICO <- as.numeric(VALORMERCADOSOF)

SOFDATAFINAL = data.frame(EDADJUGADORSOF,NOMBREJUGADORSOF,VALORMERCADOSOFNUMERICO)



Finalizada la limpieza, visualizamos utilizando view la data final de la pagina SOFIFA.

View(SOFDATAFINAL)

Finalmente, tenemos ambas paginas web con la informacion necesaria y extraida, por lo que se procede a respaldas archivos en formato csv.

### Respaldar archivo en formato csv ###


Con los siguientes comandos:

setwd("C:/Users/Amira/Documents/Scrapping_futbol")
write.csv(MKTjugadoresLIMPIO, file = "DATA_SELECCION_CHILENA_TRANSFERMKT.csv")


setwd("C:/Users/Amira/Documents/Scrapping_futbol")
write.csv(SOFjugadoresLIMPIO, file = "DATA_SELECCION_CHILENA_SOFIFA.csv")


Posterior al respaldo de los archivos se comienza la siguiente etapa llamada:

### Exploracion y visualizacion de data y tablas ###

En grandes ragos, podemos concluir que los datas frames realizados corresponden a los jugadores de la seleccion chile en la vida real y en el juego fifa.

Se procede a explorar las tablas con el comando table() con las variables extraidas


Tablas de los jugadores en la realidad.

table(MKTDATAFINAL$EDADJUGADORMKT)
table(MKTDATAFINAL$NOMBREJUGADORMKT)
table(MKTDATAFINAL$VALORMERCADOMKT)

Tablas de los jugadores en el juego FIFA.

table(SOFDATAFINAL$EDADJUGADORSOF)
table(SOFDATAFINAL$NOMBREJUGADORSOF)
table(SOFDATAFINAL$VALORMERCADOSOF)


Se procede a la siguiente etapa:

### Regresiones y graficos ###

### PAGINA TRANSFEMKT ###


La tabla de los jugadores de la seleccion chilena esta compuesto por 23 jugadores,con su respectivo valor de mercado en la vida real

table(MKTDATAFINAL$VALORMERCADOMKT)

Se procede a realizar regresion lineal con la vribles edad y valor de mercado y al aplicar el summary podemos determinar si las variables son significativas y si son consistentes al modelo

Regresion1 <- lm(EDADJUGADORMKT ~ VALORMERCADOMKT, data = MKTDATAFINAL)

summary(Regresion1)

Con los siguientes comandos podemos visualizar el comportamiento de las variables.

plot(MKTDATAFINAL$EDADJUGADORMKT, MKTDATAFINAL$VALORMERCADOMKT, xlab='EDADJUGADOMKT', ylab='VALORMERCADOMKT') 

abline(Regresion1)

plot(MKTDATAFINAL$EDADJUGADORMKT, MKTDATAFINAL$VALORMERCADOMKT,col="RED",xlab="EDADJUGADOMKT",ylab="VALORMERCADOMKT",type="p")


Otros graficos

grafica1= ggplot(MKTDATAFINAL, aes(VALORMERCADOMKT,EDADJUGADORMKT))

grafica1 + geom_point()

ggplot(MKTDATAFINAL,aes(x=VALORMERCADOMKT)) + geom_bar(fil="Red")

Finalizadndo los graficos y las regresiones se procede a realizar el mismo procedimiento con la siguiente pagina.



### Regresiones y graficos ###

### PAGINA SOFIFA ###

Esta tabla esta compuesto tambien por 23 jugadores de la seleccion chilena con su respectivo valor de mercado. 
Se procede a realizar la segunda regresion, con el objetivo similar al paso anterior de verificar si el modelo es significativo en las mismas variables, edad y valor de mercado pero en el juego FIFA.

table(SOFDATAFINAL$VALORMERCADOSOF)

Regresion2 <- lm(EDADJUGADORSOF ~ VALORMERCADOSOF, data = SOFDATAFINAL)

summary(Regresion2)

Se aplica los siguientes graficos, con el objetivo de visualizar el comportamiento de las variables

plot(SOFDATAFINAL$EDADJUGADORSOF, SOFDATAFINAL$VALORMERCADOSOF, xlab='EDADJUGADORSOF', ylab='VALORMERCADOSOF') 

abline(Regresion2)

plot(SOFDATAFINAL$EDADJUGADORSOF, SOFDATAFINAL$VALORMERCADOSOF,col="GREEN",xlab="EDADJUGADORSOF",ylab="VALORMERCADOSOF",type="p")


Se termina la fase anterior y se procede a realizar funciones.

### Funciones if/else/print ###

summary(MKTDATAFINAL)
summary(SOFDATAFINAL)

promedioedadmkt <-26.93
promedioedadsof <-25.87

if (promedioedadmkt > promedioedadsof){
  print ("El promedio de edad de la seleccion chilena es mayor a la vida real ")
} else if (promedioedadmkt < promedioedadsof){
  print ("El promedio de edad de la seleccion chilena es menor a la vida real")
}


vmercadomkt <-3143966
vmercadosof <-6514773


if (vmercadomkt > vmercadosof){
  print ("El valor de mercado de la seleccion chulena es mayor en la vida real")
} else if (vmercadomkt < vmercadosof){
  print ("El valor de mercado de la seleccion chilena es menor en la vida real")
}

Se puede concluir con la primera funcion que: "El promedio de edad de la seleccion chilena es mayor a la vida real "
con la segunda funcion se concluye que: "El valor de mercado de la seleccion chilena es menor en la vida real"



### Preguntas efectuadas del proyecto ###


¿Efectivamente el valor de mercado depende de la edad del jugador en el caso de MKT Y SOFIFA?

Debido a los graficos demostrados en el paso 5. Que muestra los jugadores de la seleccion chilena en base a la realidad. Se puede concluir que el modelo se ajusta a un 90%  en proporcion a los datos en torno a la media. Podemos visualizar que es un buen indicador pero sin embargo, Ademas podemos determinarf que  su diagrama de dispersion sus variables no son variables significativas.


Por otro lado para el caso de sofifa, en los graficos representado en el paso 6, que muestra los jugadores de la seleccion chilena en base al juego de play FIFA.  podemos concluir quee el modelo se ajusta a un 94% pero no es un modelo signficativo o de confianza por el coeficiente de correlacion.



¿Los datos del juego fifa se asemejan a la realidad de los jugadores?

A traves de la extraccion de tablas e informacion de ambas paginas  podemos evidenciar que ambas paginas muestran los mismos 23 jugadores de la seleccion chilena. Sin embargo, el valor de mercado que muestra el juego fifa es mas sobrevalorado que el real por lo que no son semejantes.


¿La informacion es sesgada?

Efectivamente la informacion es sesgada, debido a la poca extraccion de informacion de las paginas web y debido a las variables que se tomaron. 

¿Que modificaciones habria que añadir el modelo?

Un mejor manejo en el lenguaje R, ayudaria a extraer de mejor manera mucho mas informacion de las tablas. Ademas, de añadir mas variables como la posicion o campeonatos ganados.



### Conclusiones ###


En conclusion, en terminos de extraccion de informacion a traves del programa r estudio, se cumple con el objetivo. 
Sin embargo, consideramos que el modelo no es significativo debido a que su inforfmacion es mus sesgada y de muy pocas variables. Pero podemos determinar que el juego FIFA, efectivamente muestra en sus totalidad a la seleccion chilena, con sus nombre correspondiente y que el valor de mercado del juego esta sobrevalorado por el valor de mercado en la vida real de los jugadores.
Para el acceso del video explicativo dirigirse al links que se presenta a continuación https://youtu.be/hazUwf09Lq0
