# Medias-de-SSM-en-base-al-NDVI
Código de los promedios de humedad 
# ubicación del directorio de trabajo
setwd("D:/Humedad/SSM_Bir  2020/Todos los dias de SSM_1KM")

#Cargar librería raster 
library(raster)
library(maps)
library(mapview)

#Variable Topografi
#Km_Topografic <- raster("C:/Users/Usuario/Documents/Análisis de Tesis en Rstudio y SAGA GIS/Variables/Georfometria R_SAGA GIS/4 ATRIBUTOS TOPOGRAFICOS/Mask de kmeans/Mask_kmeans_topografic.tif")
#plot(Km_Topografic)
![image](https://user-images.githubusercontent.com/78845785/120053776-6aedde80-c02c-11eb-97be-715696df8d48.png)


![image](https://user-images.githubusercontent.com/78845785/120049147-623fdd00-c019-11eb-9363-07e7dad220c3.png)
![image](https://user-images.githubusercontent.com/78845785/120049181-78e63400-c019-11eb-930b-e8dc2bc7a909.png)

# Mascara de kMEAS TOPO
#MascaraKm_top <- mask(Km_Topografic, cobertura_4grupos_ESP)
#writeRaster(MascaraKm_top, file="Kmeasn_topogra4_mascaraCoberVeg.TIF")
#plot(MascaraKm_top)
Km_Topografic <- raster("D:/Georfometria R_SAGA GIS/4 ATRIBUTOS TOPOGRAFICOS/Mask Cobertura Kmeas Topografic/Kmeasn_topogra4_mascaraCoberVeg.tif")
plot(Km_Topografic)

cobertura4grupos <- shapefile("D:/Cobertura vectorial de 5 GB/Cortes de ESP/Disolve 4 grupos cobertura correcion geo.shp")

cobertura_4grupos_ESP <- spTransform(cobertura4grupos, CRS(projection("+proj=longlat +ellps=WGS84 +no_defs ")))
projection(cobertura_4grupos_ESP)
![image](https://user-images.githubusercontent.com/78845785/120049220-961b0280-c019-11eb-9efa-9c84d0bf5baf.png)

#Unificar archivos
s <- stack(list.files(pattern = ".nc"), varname="ssm")

#Extención del area de investigación
ex <- extent(-9.4, 4.4, 35.2, 43.9)
#
A <- stack(crop(s, ex))
plot(A)

# Limites
#limn <- getData('GADM', country='ESP', level=2)
Mascarassm <- mask(A, cobertura_4grupos_ESP)
plot(Mascarassm)
![image](https://user-images.githubusercontent.com/78845785/120049255-b6e35800-c019-11eb-9b76-3496d84d42b5.png)
![image](https://user-images.githubusercontent.com/78845785/120049304-d4182680-c019-11eb-9f08-22f6be949b8b.png)

#Reproyectada a 1 km, ya que estaba en 5 km
ssm_pro <- projectRaster(Mascarassm, Km_Topografic)
plot(ssm_pro)
![image](https://user-images.githubusercontent.com/78845785/120049332-ec884100-c019-11eb-8c6a-d53a0097ef48.png)

#writeRaster(Prc_pro, file="PrecipitacionESP_1km.tif")

# CORREGIR LOS LIMITES DE 0 A 100% SSM
plot(ssm_pro, zlim=c(0, 100))
![image](https://user-images.githubusercontent.com/78845785/120049372-04f85b80-c01a-11eb-9085-88129469112f.png)

plot(ssm_pro, zlim=c(0, 120))
![image](https://user-images.githubusercontent.com/78845785/120049404-20636680-c01a-11eb-8cd7-08930d613bd9.png)

plot(ssm_pro, zlim=c(100, 120))
![image](https://user-images.githubusercontent.com/78845785/120049424-33763680-c01a-11eb-9829-45b523c59368.png)

#boxplot(ssm_pro)
#ssm_pro[ssm_pro > 100] <- NA

#9 dias antes del 21 + el 21 diciembre 2019 
diaSSM_12_21na_true <- calc(ssm_pro[[1:10]], mean, na.rm=TRUE)
![image](https://user-images.githubusercontent.com/78845785/120049462-5274c880-c01a-11eb-9528-168170e08919.png)

diaSSM_12_21na_true[diaSSM_12_21na_true> 100] <- NA
![image](https://user-images.githubusercontent.com/78845785/120049491-691b1f80-c01a-11eb-95e8-f8f7b511966d.png)

plot(diaSSM_12_21na_true)
![image](https://user-images.githubusercontent.com/78845785/120049507-77693b80-c01a-11eb-8224-c90c645f612d.png)

mapview(diaSSM_12_21na_true)
![image](https://user-images.githubusercontent.com/78845785/120049562-a41d5300-c01a-11eb-84af-e74ad7c9a3a3.png)

### Al aplicar la mascara en las zonas de mayor altitud no hay datos de humedad!!!
![image](https://user-images.githubusercontent.com/78845785/120053951-7097f400-c02d-11eb-82f7-1d6a55d14133.png)


#10 dias antes del 1 + el 1 enero 2020 (diciembre tiene 31 dias)
diaSSM_22_1na_true <- calc(ssm_pro[[11:21]], mean, na.rm=TRUE)
plot(diaSSM_22_1na_true)
mapview(diaSSM_22_1na_true)

#9 dias antes del 11 + el 11 enero 2020
diaSSM_2_11na_true <- calc(ssm_pro[[22:31]], mean, na.rm=TRUE)
plot(diaSSM_2_11na_true)
mapview(diaSSM_2_11na_true)

#9 dias antes del 21 + el 21 enero 2020
diaSSM_12_21na_true <- calc(ssm_pro[[32:41]], mean, na.rm=TRUE)
plot(diaSSM_12_21na_true)

#10 dias antes del 1 + el 1 Febrero 2020 (11 dias ya que enero tiene 31 dias)
diaSSM_22ene_1Febna_true <- calc(ssm_pro[[42:52]], mean, na.rm=TRUE)
plot(diaSSM_22ene_1Febna_true)

#9 dias antes del 11 + el 11 Febrero 2020
diaSSM_2_11Feb_na_true <- calc(ssm_pro[[53:62]], mean, na.rm=TRUE)
plot(diaSSM_2_11Feb_na_true)

#9 dias antes del 21 + el 21 Febrero 2020
diaSSM_12_21Feb_na_true <- calc(ssm_pro[[63:72]], mean, na.rm=TRUE)
plot(diaSSM_12_21Feb_na_true)

#8 dias antes del 1 + el 1 marzo 2020 (8 dias porque febrero tiene 29 dias)
diaSSM_22Feb_1Marzo_na_true <- calc(ssm_pro[[73:81]], mean, na.rm=TRUE)
plot(diaSSM_22Feb_1Marzo_na_true)

#9 dias antes del 11 + el 11 marzo 2020 
diaSSM_2_11Marzo_na_true <- calc(ssm_pro[[82:91]], mean, na.rm=TRUE)
plot(diaSSM_2_11Marzo_na_true)

#9 dias antes del 21 + el 11 marzo 2020 
diaSSM_12_21Marzo_na_true <- calc(ssm_pro[[92:101]], mean, na.rm=TRUE)
plot(diaSSM_12_21Marzo_na_true)

#10 dias antes del 1 + el 1 abril 2020 (11 dias, es por los meses de 31 dias)
diaSSM_22Marzo_1abr_na_true <- calc(ssm_pro[[102:112]], mean, na.rm=TRUE)
plot(diaSSM_22Marzo_1abr_na_true)

#9 dias antes del 11 + el 11 abril 2020 
diaSSM_2_11abr_na_true <- calc(ssm_pro[[113:122]], mean, na.rm=TRUE)
plot(diaSSM_2_11abr_na_true)

#9 dias antes del 21 + el 21 abril 2020 
diaSSM_12_21abr_na_true <- calc(ssm_pro[[123:132]], mean, na.rm=TRUE)
plot(diaSSM_12_21abr_na_true)

#9 dias antes del 1 + el 1 Mayo 2020 
diaSSM_22abr_1mayo_na_true <- calc(ssm_pro[[133:142]], mean, na.rm=TRUE)
plot(diaSSM_22abr_1mayo_na_true )

#9 dias antes del 11 + el 11 Mayo 2020 
diaSSM_2_11mayo_na_true <- calc(ssm_pro[[143:152]], mean, na.rm=TRUE)
plot(diaSSM_2_11mayo_na_true)

#9 dias antes del 21 + el 21 Mayo 2020 
diaSSM_12_21mayo_na_true <- calc(ssm_pro[[153:162]], mean, na.rm=TRUE)
plot(diaSSM_12_21mayo_na_true)

#10 dias antes del 1 + el 1 jUNIO 2020 (11 dias ya que mayo tiene 31 dia)
diaSSM_22mayo_1Juniona_true <- calc(ssm_pro[[163:173]], mean, na.rm=TRUE)
plot(diaSSM_22mayo_1Juniona_true)

#9 dias antes del 11 + el 11 jUNIO 2020 
diaSSM_2_11Juniona_true <- calc(ssm_pro[[174:183]], mean, na.rm=TRUE)
plot(diaSSM_2_11Juniona_true)

#9 dias antes del 21 + el 21 jUNIO 2020 
diaSSM_12_21Juniona_true <- calc(ssm_pro[[184:193]], mean, na.rm=TRUE)
plot(diaSSM_12_21Juniona_true)

#9 dias antes del 1 + el 1 julio 2020 
diaSSM_22Juni_1julioona_true <- calc(ssm_pro[[194:203]], mean, na.rm=TRUE)
plot(diaSSM_22Juni_1julioona_true)

#9 dias antes del 11 + el 11 julio 2020 
diaSSM_2_11julioona_true <- calc(ssm_pro[[204:213]], mean, na.rm=TRUE)
plot(diaSSM_2_11julioona_true)

#9 dias antes del 21 + el 21 julio 2020 
diaSSM_12_21julioona_true <- calc(ssm_pro[[214:223]], mean, na.rm=TRUE)
plot(diaSSM_2_11julioona_true)

#10 dias antes del 1 + el 1 Agosto 2020 (11 dias ya que julio tiene 31 dias)
diaSSM_22julio_1agoona_true <- calc(ssm_pro[[224:234]], mean, na.rm=TRUE)
plot(diaSSM_22julio_1agoona_true)

#9 dias antes del 11 + el 11 Agosto 2020 
diaSSM_2_11agostona_true <- calc(ssm_pro[[235:244]], mean, na.rm=TRUE)
plot(diaSSM_2_11agostona_true)

#9 dias antes del 21 + el 21 Agosto 2020 
diaSSM_12_21agostona_true <- calc(ssm_pro[[235:254]], mean, na.rm=TRUE)
plot(diaSSM_12_21agostona_true)

#10 dias antes del 1 + el 1 Septiembre 2020 (11 dias ya que agosto tiene 31 dias)
diaSSM_22agosto_1Sepna_true <- calc(ssm_pro[[255:265]], mean, na.rm=TRUE)
plot(diaSSM_22agosto_1Sepna_true)

#9 dias antes del 11 + el 11 Septiembre 2020 
diaSSM_2_11septina_true <- calc(ssm_pro[[266:275]], mean, na.rm=TRUE)
plot(diaSSM_2_11septina_true)

#9 dias antes del 21 + el 21 Septiembre 2020 
diaSSM_12_21septina_true <- calc(ssm_pro[[276:285]], mean, na.rm=TRUE)
plot(diaSSM_12_21septina_true)

#9 dias antes del 1 + el 1 Octubre 2020 
diaSSM_22septi_1octuna_true <- calc(ssm_pro[[286:295]], mean, na.rm=TRUE)
plot(diaSSM_22septi_1octuna_true)

#9 dias antes del 11 + el 11 Octubre 2020 
diaSSM_2_11octuna_true <- calc(ssm_pro[[306:315]], mean, na.rm=TRUE)
plot(diaSSM_2_11octuna_true)

#9 dias antes del 21 + el 21 Octubre 2020 
diaSSM_12_21octuna_true <- calc(ssm_pro[[316:325]], mean, na.rm=TRUE)
plot(diaSSM_12_21octuna_true )

#10 dias antes del 1 + el 1 Noviembre 2020 (11 dias ya que octubre tiene 31 dias)
diaSSM_22octubre_1Novna_true <- calc(ssm_pro[[326:336]], mean, na.rm=TRUE)
plot(diaSSM_22octubre_1Novna_true)

#9 dias antes del 11 + el 11 Noviembre 2020 
diaSSM_2_11Novna_true <- calc(ssm_pro[[337:346]], mean, na.rm=TRUE)
plot(diaSSM_2_11Novna_true)

#9 dias antes del 21 + el 21 Noviembre 2020 
diaSSM_12_21Novna_true <- calc(ssm_pro[[347:356]], mean, na.rm=TRUE)
plot(diaSSM_12_21Novna_true)

#9 dias antes del 1 + el 1 Diciembre 2020 
diaSSM_22nov_1dicna_true <- calc(ssm_pro[[357:366]], mean, na.rm=TRUE)
plot(diaSSM_22nov_1dicna_true)

#9 dias antes del 11 + el 11 Diciembre 2020 
diaSSM_2_11dicna_true <- calc(ssm_pro[[367:376]], mean, na.rm=TRUE)
plot(diaSSM_2_11dicna_true)

#9 dias antes del 21 + el 21 Diciembre 2020 
diaSSM_12_21dicna_true <- calc(ssm_pro[[377:386]], mean, na.rm=TRUE)
plot(diaSSM_12_21dicna_true)



#Guardando como imagen
writeRaster(diaSSM_23_1na_true, file="PromediodiaSSM_23diceimbre2019_1_enero_2020_1.tif")
writeRaster(diaSSM_2_11na_true, file="PromedioDíaSSM_2al11enero_2020_11.tif")
writeRaster(diaSSM_12_21na_true, file="PromedioDíaSSM_12al21enero_2020_21.tif")
writeRaster(diaSSM_22ene_1Febna_true, file="PromedioDíaSSM_22eneral1Febrero_2020_1.tif")
writeRaster(diaSSM_2_11Feb_na_true, file="PromedioDíaSSM_2_11Febrero_2020_11.tif")
writeRaster(diaSSM_12_21Feb_na_true, file="PromedioDíaSSM_12_21Febrero_2020_21.tif")
writeRaster(diaSSM_22Feb_1Marzo_na_true, file="PromedioDíaSSM_22Feb_1Marzo2020_1.tif")
writeRaster(diaSSM_2_11Marzo_na_true, file="PromedioDíaSSM_2_11Marzo2020_11.tif")
writeRaster(diaSSM_12_21Marzo_na_true, file="PromedioDíaSSM_12_21Marzo2020_21.tif")
writeRaster(diaSSM_22Marzo_1abr_na_true, file="PromedioDíaSSM_22Marzo_1Abr2020_1.tif")
writeRaster(diaSSM_2_11abr_na_true, file="PromedioDíaSSM_2_11Abr2020_11.tif")
writeRaster(diaSSM_12_21abr_na_true, file="PromedioDíaSSM_12_21Abr2020_21.tif")
writeRaster(diaSSM_22abr_1mayo_na_true, file="PromedioSSM_22abr_1mayo2020_1.tif")
writeRaster(diaSSM_2_11mayo_na_true, file="PromedioDíaSSM_2_11mayo2020_11.tif")
writeRaster(diaSSM_12_21mayo_na_true, file="PromedioDíaSSM_12_21mayo2020_21.tif")
writeRaster(diaSSM_22mayo_1Juniona_true, file="PromedioSSM_22mayo_1Junio2020_1.tif")
writeRaster(diaSSM_2_11Juniona_true, file="PromedioDíaSSM_2_11Junio2020_11.tif")
writeRaster(diaSSM_12_21Juniona_true, file="PromedioDíaSSM_12_21Junio2020_21.tif")
writeRaster(diaSSM_22Juni_1julioona_true, file="PromedioDíaSSM_22Juni_1julio2020_1.tif")
writeRaster(diaSSM_2_11julioona_true, file="PromedioDíaSSM_2_11julio2020_11.tif")
writeRaster(diaSSM_12_21julioona_true, file="PromedioDíaSSM_12_21julio2020_21.tif")
writeRaster(diaSSM_22julio_1agoona_true, file="PromedioDíaSSM_22julio_1ago2020_1.tif")
writeRaster(diaSSM_2_11agostona_true, file="PromedioDíaSSM_2_11agosto2020_11.tif")
writeRaster(diaSSM_12_21agostona_true, file="PromedioDíaSSM_12_21agosto2020_21.tif")
writeRaster(diaSSM_22agosto_1Sepna_true, file="PromedioDíaSSM_22agosto_1Sep2020_1.tif")
writeRaster(diaSSM_2_11septina_true, file="PromedioDíaSSM_2_11septi2020_11.tif")
writeRaster(diaSSM_12_21septina_true, file="PromedioDíaSSM_12_21septi2020_21.tif")
writeRaster(diaSSM_22septi_1octuna_true, file="PromedioSSM_22septi_1octu2020_1.tif")
writeRaster(diaSSM_2_11octuna_true, file="PromedioDíaSSM_2_11octu2020_11.tif")
writeRaster(diaSSM_12_21octuna_true, file="PromedioDíaSSM_12_21octu2020_21.tif")
writeRaster(diaSSM_22octubre_1Novna_true, file="PromedioSSM_22octubre_1Nov2020_1.tif")
writeRaster(diaSSM_2_11Novna_true, file="PromedioDíaSSM_2_11Nov2020_11.tif")
writeRaster(diaSSM_12_21Novna_true, file="PromedioDíaSSM_12_21NovNov2020_21.tif")
writeRaster(diaSSM_22nov_1dicna_true, file="PromedioDíaSSM_22nov_1dic2020_1.tif")
writeRaster(diaSSM_2_11dicna_true, file="PromedioDíaSSM_2_11dic2020_11.tif")
writeRaster(diaSSM_12_21dicna_true, file="PromedioDíaSSM_12_21dic2020_21.tif")
writeRaster(diaSSM_22_31dicna_true, file="PromedioDíaSSM_22_31dic2020_31.tif")
