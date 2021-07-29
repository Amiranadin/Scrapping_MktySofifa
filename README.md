# Scrapping_MktySofifa
El siguiente repositorio, consiste en la extracción de información de la seleccion chilena de futbol vs la seleccion chilena mostrada en el juego FIFA. Esta informacion extraida por dos paginas web. Con el objetivo de identificar si el juego muestra exactamente la realidad de los jugadores.


En los siguientes sitios web extraeremos la información necesaria para la investigación. 


La primera pagina web, muestra informacion acerca de los jugadores en la vida real. 
https://www.transfermarkt.es/chile/kader/verein/3700/saison_id/2020

La segunda pagina web, muestra informacion de los jugadores en el juego de play  FIFA.

https://sofifa.com/team/111459/chile/?hl=es-ES


Para iniciar la investigacion en el programa, lo primero fue instalar las siguientes paqueterias, para poder utilizar ciertos comandos a medida que la investigacion avanza.


Commands:

library("rvest")
install.packages("readxl")
install.packages("dplyr")

library(readxl)
library(dplyr)



Luego, el primer paso es realizar la extracion de informacion de la primera mpagina web llamada: Transfermarkt. Es decir, se designa por nombre chimkt y se utiliza el comando real_html para poder leer la pagina web.

##Primera pagina web##

Abiendo pagina web:

chimkt <- read_html("https://www.transfermarkt.es/chile/kader/verein/3700/saison_id/2020")

A continuacion, se utiliza el print para imprimir y verifica si efectivamente nos muestra la pagina web.


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

Posterior a la asignacion de nombres, eliminamos las columnas 2,3,7 para que finalmente nos quedaran olamente las que nos sirven


Comandos para eliminar columnas 2.3 y 7.

MKTjugadores["2"] = NULL
MKTjugadores["3"] = NULL
MKTjugadores["7"] = NULL

MKTjugadoresLIMPIO <- na.omit(MKTjugadores)

#Para realizar el analisis de los jugadores, evidenciamos que el valor de mercado de la tabla 
#esta sucio debido a que es un formato texto y numerico.
#Necesitamos que sea en formato numerico, para un analisis mas especifico.


#Formato final de la tabla

EDADJUGADORMKT = MKTjugadoresLIMPIO$EDAD
NOMBREJUGADORMKT = MKTjugadoresLIMPIO$NOMBRE
VALORMERCADOMKT = c("3800000","1000000","450000","12000000","6000000","2000000","1500000","500000","1800000","950000","9000000","4000000","3100000","1500000","1000000","6000000","3500000","800000","1400000","900000","5000000","850000","2000000","3000000","7500000","7000000","4000000","600000","25000")
VALORMERCADOMKTNUMERICO <- as.numeric(VALORMERCADOMKT)

MKTDATAFINAL = data.frame(EDADJUGADORMKT,NOMBREJUGADORMKT,VALORMERCADOMKTNUMERICO)

##Visualizando data final##

View(MKTDATAFINAL)



####################  PASO 2 ########################
#               SEGUNDA PAGINA WEB                  #



#se procede a extraer la informaciÃ³n de la segunda pagina web


chisof <- read_html("https://sofifa.com/team/111459/chile/?hl=es-ES")


print(html_text(chisof))

#Para extraer la informaciÃ³n se utiliza la class "list", debido a que al igual
#que nuestra primera pagina, no poseen ID.

TABLAjugadoresSOF <- html_nodes(chisof,css = ".list")


SOFjugadoresLIMPIO <- html_table(TABLAjugadoresSOF)[[1]]



print(html_text(TABLAjugadoresSOF[[1]]))

#Se procede a limpiar la data, es decir las columnas debido a que la data 
#tablajudaoressof es una data sucia, que no servira para el objetivo final

SOFjugadoresLIMPIO["X1"] = NULL
SOFjugadoresLIMPIO["X4"] = NULL
SOFjugadoresLIMPIO["X5"] = NULL
SOFjugadoresLIMPIO["X8"] = NULL
SOFjugadoresLIMPIO["X9"] = NULL
SOFjugadoresLIMPIO["X10"] = NULL

names(SOFjugadoresLIMPIO)= c("NOMBRE", "EDAD", "POSICION", "VALOR DE MERCADO")


#Data final limpia debido a que el precio de valor de mercado estaba en formato
#numerico y texto. Se repite procedimiento a la primera pagina.


EDADJUGADORSOF = SOFjugadoresLIMPIO$EDAD

NOMBREJUGADORSOF = SOFjugadoresLIMPIO$NOMBRE

VALORMERCADOSOF = c("1200,000","4100000","2700000","2800000","10500000","16000000","6000000","18000000","6000000","18000000","3100000","2100000","4300000","2800000","0","0","1900000","7000000","0","475000","450000","36000000","1100000")

as.numeric(VALORMERCADOSOF)

VALORMERCADOSOFNUMERICO <- as.numeric(VALORMERCADOSOF)

SOFDATAFINAL = data.frame(EDADJUGADORSOF,NOMBREJUGADORSOF,VALORMERCADOSOFNUMERICO)

#Visualizacion de la data limpia.

View(SOFDATAFINAL)


####################  PASO 3 ########################
#         RESPALDAR ARCHIVO EN FORMATO CSV          #


####LUEGO DE LIMPIAR LA DATA SE PROCEDE A RESPALDAR LOS ARCHIVOS EN FORMATO CSV###

setwd("C:/Users/Amira/Documents/Scrapping_futbol")
write.csv(MKTjugadoresLIMPIO, file = "DATA_SELECCION_CHILENA_TRANSFERMKT.csv")


setwd("C:/Users/Amira/Documents/Scrapping_futbol")
write.csv(SOFjugadoresLIMPIO, file = "DATA_SELECCION_CHILENA_SOFIFA.csv")


####################  PASO 4 ########################
#   EXPLORACION Y VISUALIZACION DE DATA Y TABLAS    #



#En grandes ragos, podemos concluir que los datas frames realizados 
#corresponden a los jugadores de la seleccion chile en la vida real y en
#el juego fifa.


#Se procede a explorar las tablas con el comando table() con las variables extraidas


#Tablas de los jugadores en la realidad.

table(MKTDATAFINAL$EDADJUGADORMKT)
table(MKTDATAFINAL$NOMBREJUGADORMKT)
table(MKTDATAFINAL$VALORMERCADOMKT)

#Tablas de los jugadores en el juego FIFA.

table(SOFDATAFINAL$EDADJUGADORSOF)
table(SOFDATAFINAL$NOMBREJUGADORSOF)
table(SOFDATAFINAL$VALORMERCADOSOF)


####################  PASO 5 ########################
#             REGRESIONES/GRAFICOS/ESTADISTICA      #

#             PAGINA TRANSFEMKT                     #



#Tabla de los jugadores de la seleccion chilena
#esta compuesto por 23 jugadores,con su respectivo valor de mercado en la vida real

table(MKTDATAFINAL$VALORMERCADOMKT)


Regresion1 <- lm(EDADJUGADORMKT ~ VALORMERCADOMKT, data = MKTDATAFINAL)

summary(Regresion1)

plot(MKTDATAFINAL$EDADJUGADORMKT, MKTDATAFINAL$VALORMERCADOMKT, xlab='EDADJUGADOMKT', ylab='VALORMERCADOMKT') 

abline(Regresion1)

plot(MKTDATAFINAL$EDADJUGADORMKT, MKTDATAFINAL$VALORMERCADOMKT,col="RED",xlab="EDADJUGADOMKT",ylab="VALORMERCADOMKT",type="p")


#Otros graficos

grafica1= ggplot(MKTDATAFINAL, aes(VALORMERCADOMKT,EDADJUGADORMKT))

grafica1 + geom_point()

ggplot(MKTDATAFINAL,aes(x=VALORMERCADOMKT)) + geom_bar(fil="Red")


####################  PASO 6 ########################
#             REGRESIONES/GRAFICOS/ESTADISTICA      #

#             PAGINA SOFIFA                     #


#Esta tabla esta compuesto tambien por 23 jugadores de la seleccion chilena con su respectivo valor de mercado.

table(SOFDATAFINAL$VALORMERCADOSOF)

Regresion2 <- lm(EDADJUGADORSOF ~ VALORMERCADOSOF, data = SOFDATAFINAL)

summary(Regresion2)

plot(SOFDATAFINAL$EDADJUGADORSOF, SOFDATAFINAL$VALORMERCADOSOF, xlab='EDADJUGADORSOF', ylab='VALORMERCADOSOF') 

abline(Regresion2)

plot(SOFDATAFINAL$EDADJUGADORSOF, SOFDATAFINAL$VALORMERCADOSOF,col="GREEN",xlab="EDADJUGADORSOF",ylab="VALORMERCADOSOF",type="p")


####################  PASO 7 ########################
#                FUNCIONES                          #

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


####################  PASO 8 ########################
#           PREGUNTAS EFECTUADAS DEL PROYECTO      #


#Efectivamente el valor de mercado depende de la edad del jugador en el caso de MKT Y SOFIFA?

#Debido a los graficos demostrados en el paso 5. Que muestra los jugadores de la seleccion chilena
#en base a la realidad. Se puede concluir que el modelo se ajusta a un 90% 
#en proporcion a los datos en torno a la media. Podemos visualizar que es un buen indicador
#pero sin embargo, Ademas podemos determinarf que  su diagrama de dispersion sus variables no son variables significativas.


#por otro lado para el caso de sofifa, en los graficos representado en el paso 6, que muestra los jugadores
#de la seleccion chilena en base al juego de play FIFA. 
#podemos concluir quee el modelo se ajusta a un 94% pero no es un modelo signficativo o de confianza por el coeficiente de correlacion.




#Los datos del juego fifa se asemejan a la realidad de los jugadores?
#a traves de la extraccion de tablas e informacion de ambas paginas 
#podemos evidenciar que ambas paginas muestran los mismos 23 jugadores de la seleccion chilena
#sin embargo, el valor de mercado que muestra el juego fifa es mas sobrevalorado que el real por lo que
#no son semejantes.


#La informacion es sesgada?
# Efectivamente la informacion es sesgada, debido a la poca extraccion de informacion de las paginas web
# y debido a las variables que se tomaron. 

#Que modificaciones habria que añadir el modelo?
#Un mejor manejo en el lenguaje R, ayudaria a extraer de mejor manera mucho mas informacion de las tablas
#ademas, de añadir mas variables como la posicion o campeonatos ganados.



####################  PASO 9 ########################
#                     Conclusiones                  #


#En conclusion, en terminos de extraccion de informacion a traves del programa r estudio, se cumple con el objetivo. 
#Sin embargo, consideramos que el modelo no es significativo debido a que su inforfmacion
#es mus sesgada y de muy pocas variables. Pero podemos determinar que el juego FIFA, efectivamente
#muestra en sus totalidad a la seleccion chilena, con sus nombre correspondiente y que el valor de mercado del juego esta sobrevalorado
#por el valor de mercado en la vida real de los jugadores.
