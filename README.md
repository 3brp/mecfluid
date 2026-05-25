# Simulación
## Pre-processing
Con el archivo `condcontorno.ntop` (nTopology software) se puede introducir un cuerpo CAD y se genera automáticamente un mesh volumétrico de quads. Además, se pueden añadir condiciones de contorno en base a las caras del archivo CAD y el número de quads de meta a generar. Por último, se genera una malla FE a partir de esas condiciones de contorno y la malla volumétrica en formato `.msh`.

## Processing
### Generación Malla Foam
Una vez generada la malla FE, se pone en la carpeta del trabajo. Se abre una terminal y se `cd` a la ruta de acceso de dicha carpeta. Entonces, se invoca el comando:

```
fluentMeshToFoam malla.msh
```
y se comprueba que no halla habido problemas en la operación. Además, en la carpeta `...\constant\polyMesh` se habrán generado ciertos archivos en relación a la malla. Aquí dentro, se ha de comprobar el archivo `boundary` para asegurarse de que las condiciones de contorno definidas anteriormente se mantienen.


### Chequeo Malla
Se comprueba que la malla no tenga problemas invocando:
```
checkMesh
```
Si no da problemas (el número de fallos se indica al final de la operación), se puede pasar al siguiente paso.


### Opcional
Si se ha importado la malla en unidades incorrectas, se puede escalar usando:
```
transformPoints -scale "(1 1 1)"
```
### Opcional 2
En `...\system\setFieldsDict` se ha de establecer el recuadro donde se quiere que haya agua como condición inicial (si es que se quiere), y luego usar el comando:
```
setFields
```
### Simulación en paralelo
En `...\system\decomposeParDict` se puede establecer que la simulación se ejecute en paralelo, especificando el número de cortes en cada eje que se quiere efectuar y el número de procesadores a usar. Para activarlo:
```
decomposePar
```
y se lanza la simulación tal que:
```
mpirun -np 4 interFoam -parallel > log.interFoam &
```
donde `log.interFoam` es un archivo donde se va a guardar el log de la simulación, ya que se efectuará en segundo plano. 
Por último, usar la instrucción:
```
reconstructPar
```
Para reconstruir los datos generados.

### Simulación normal
Para lanzar la simulación, simplemente efectuar:
```
interFoam
```

### Borrar datos
En caso de querer borrar los datos de la simulación para volver a lanzarla desde 0 usar:
```
 foamListTimes -rm
 rm -rf processor*
```

## Post-processing
He usado ParaView para la visualización de los datos pero también se puede exportar a formatos más comunes como VTK
