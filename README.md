# Guía de Características Avanzadas de AWS CloudFormation
¡Bienvenido a la guía de características avanzadas de AWS CloudFormation! Este documento proporciona una visión general de funcionalidades extendidas que te ayudarán a crear y gestionar tus plantillas de infraestructura de manera más eficiente y modular.

---
CloudFormation es un servicio complejo que proporciona muchas características. Hasta ahora en este curso, ha visto varias clases en vídeo, laboratorios prácticos y discusiones sobre los aspectos básicos de CloudFormation (como las secciones Recursos, Descripción y Parámetros). Esta lectura complementaria cubre algunas de las características ampliadas que ofrece CloudFormation (como Refs, Funciones, Mapeo y Pilas anidadas) tanto para informarle como para proporcionarle recursos adicionales si desea profundizar en CloudFormation.
## Índice

1. [Referencias (Ref)](#referencias-ref)
2. [Funciones Intrínsecas](#funciones-intrínsecas)
3. [Mapeos](#mapeos)
4. [Pilas Anidadas](#pilas-anidadas)
5. [Recursos Adicionales](#recursos-adicionales)

---

## Referencias (Ref)

Imagine que desea crear tanto una nube privada virtual (VPC) como un grupo de seguridad utilizando la misma plantilla. Quiere que el grupo de seguridad forme parte de la VPC. Cuando declara una plantilla, aún no está creando recursos, los está definiendo (recuerde que las plantillas de CloudFormation sondeclarativas). Los recursos sólo se crean efectivamente cuando crea una pila. Sin embargo, necesita un ID de VPC para adjuntar una instancia de grupo de seguridad a una VPC, pero la VPC aún no existe. ¿Cómo resuelve este problema? 

La respuesta es utilizar Ref. Empiece declarando la VPC como de costumbre. Observe elID lógico personalizado llamado miVPC:

**Ejemplo:**

```yaml
myVPC:
  Type: AWS::EC2::VPC
  Properties:
    CidrBlock: 10.0.0.0/16
```
A continuación, cuando declare el grupo de seguridad, puede hacer referencia al ID lógico myVPC como valor de la propiedad VpcId. Para ello, utilice la siguiente sintaxis: !Ref myVPC

```yaml
InstanceSecurityGroup:
  Type: AWS::EC2::SecurityGroup
  Properties:
    GroupDescription: Allow http to client host
    VpcId: !Ref myVPC
    SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: 0.0.0.0/0
    SecurityGroupEgress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: 0.0.0.0/0
```
En resumen, puede utilizar !Ref cuando desee recuperar el ID lógico de un recurso. Es una función que se utiliza a menudo cuando se escriben scripts.

FUNCIONES

Nota: Las funciones descritas en esta sección sonfunciones intrínsecas de CloudFormation, y no son lo mismo que las funciones de AWS Lambda. 

CloudFormation proporciona varias funciones incorporadas (intrínsecas) para ayudarle a gestionar sus pilas. Puede utilizar funciones intrínsecas en sus plantillas para asignar valores a propiedades que no están disponibles hasta el tiempo de ejecución (o hasta que se crea la pila). 

Como leyó en la sección anterior, puede utilizar !Ref cuando desee hacer referencia al ID lógico de otros recursos que hayan sido creados por la misma plantilla. Sin embargo, ¿qué ocurre si quiere referirse a algo quenoes el ID lógico? 

La función intrínseca Fn::GetAtt devuelve el valor de un atributo de un recurso de la plantilla. Puede utilizarla de la siguiente forma abreviada:

!GetAtt logicalNameOfResource.attributeName

Por ejemplo, supongamos que desea crear un grupo de seguridad similar al siguiente:
```yaml
InstanceSecurityGroup:
  Type: AWS::EC2::SecurityGroup
  Properties:
    GroupDescription: Allow http to client host
    VpcId: !Ref myVPC
    SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: 0.0.0.0/0
    SecurityGroupEgress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: 0.0.0.0/0
```
Este recurso tiene muchos atributos, como ToPort, IpProtocol, CidrIp, etc. Si desea obtener uno de estos valores para utilizarlo en algún otro lugar de la plantilla de CloudFormation, puede utilizar la función intrínseca Fn::GetAtt. 

Para obtener más información y ejemplos de la función intrínseca Fn::GetAtt, consulte la página
FN::GetAtt
 en laGuía del usuario de AWS CloudFormation. 

Al declarar una plantilla de CloudFormation, puede utilizar otras funciones intrínsecas, entre las que se incluyen las siguientes::

- [Fn::Base64](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-base64.html)
  
- [Fn::Cidr](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-cidr.html)
  
- [Funciones de condición](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-conditions.html)
  
- [Fn::FindInMap](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html)
  
- [Fn::ForEach](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-foreach.html)
  
- [Fn::GetAtt](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getatt.html)
  
- [Fn::GetAZs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getavailabilityzones.html)
  
- [Fn::ImportValue](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-importvalue.html)

- [Fn::Join](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-join.html)

- [Fn::Longitud](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-length.html)

- [Fn::Seleccionar](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-select.html)

- [Fn::Dividir](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-split.html)

- [Fn::Sub](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-sub.html)

- [Fn::ToJsonString](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-ToJsonString.html)

- [Fn::Transformar](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-transform.html)

Mediante el uso de funciones intrínsecas en las plantillas de CloudFormation, puede definir los recursos en relación unos con otros. Las funciones intrínsecas de CloudFormation añaden así más modularidad y enriquecen las capacidades de sus plantillas.

MAPPING

La sección opcional Mappings empareja una clave con un conjunto correspondiente de valores nombrados. Como ejemplo, suponga que desea establecer valores basados en una Región. Puede crear un mapeo que utilice el nombre de la Región como clave, y que contenga los valores que desea definir para cada Región específica. Para recuperar valores en un mapa, utilice la función intrínseca Fn::FindInMap. 

Si desea lanzar una nueva instancia EC2, necesita un ID de AMI (Imagen de máquina de Amazon). Los ID de AMI son únicos para cada región. Por ejemplo, el ID de AMI para Windows Server en la Región us-east-1 (o N. Virginia) es diferente del ID de AMI para Windows Server en la Región sa-east-1 (o São Paulo). 

Considere esta cuestión: En lugar de codificar de forma rígida los ID de AMI en plantillas específicas de la Región, ¿cómo podría crear una plantilla de CloudFormation resistente que pueda utilizar en dos Regiones diferentes, o incluso en varias Regiones? 

La respuesta es utilizar mapeos. 

En primer lugar, se empieza por declarar el mapeo. La siguiente asignación muestra ejemplos de ID de
[AMI](https://aws.amazon.com/linux/amazon-linux-2023/features/) para la [AMI de Amazon Linux 2023](https://aws.amazon.com/linux/amazon-linux-2023/features/) 

```yaml
Mappings: 

  RegionMap: 

    us-east-1: 

      "AMIID": "ami-041feb57c611358bd"  

    sa-east-1: 

      "AMIID": "ami-03376b64cbb65efec" 

    eu-west-1: 

      "AMIID": "ami-0b9fd8b55a6e3c9d5" 

    ap-southeast-1: 

      "AMIID": "ami-0c85e22f0829284aa" 

    ap-northeast-1: 

      "AMIID": "ami-0fd8f5842685ca887"
```

Después de definir la asignación, utilice la función intrínseca Fn::FindInMap para encontrar y devolver un valor en la asignación. En la declaración de recursos de la instancia, tiene un aspecto similar al siguiente ejemplo (observe la propiedad ImageId):

```yaml
Resources: 
  myEC2Instance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMIID]
      InstanceType: m1.small
```
Nota:!FindInMap es el formato de la función intrínseca FN::FindInMap cuando se utiliza en plantillas YAML de CloudFormation. En las plantillas de CloudFormation JSON, la función intrínseca FN::FindInMap tendría el siguiente formato:

```yaml
"ImageId" : { "Fn::FindInMap" : [ "RegionMap", { "Ref" : "AWS::Region" }, "AMIID"]},
```
Información adicional: Para soportar una mayor modularidad en la declaración de recursos, puede utilizar pseudoparámetros(como AWS::Regionen los ejemplos de código anteriores). Los pseudoparámetros son parámetros predefinidos por CloudFormation, por lo que no necesita declararlos en su plantilla. Puede utilizarlos en la declaración del recurso como argumento de la función Ref del mismo modo que utilizaría un parámetro normal. En el ejemplo de código anterior para la sección Recursos, el pseudoparámetro AWS::Region se sustituirá por la región actual en la que se crea la pila. Por ejemplo, si una pila se crea enus-east-1, entoncesus-east-1 es el valor que se utiliza para encontrar en la Región en el mapeo. La siguiente lista describe algunos pseudoparámetros de uso común:

- AWS::Region: Devuelve una cadena que representa la Región en la que se está creando el recurso de englobamiento (comous-west-2)

- AWS::StackId: Devuelve el ID de la pila tal y como se especifica en el comando aws cloudformation create-stack (comoarn:aws:cloudformation:us-west-2:021345abcdef6789:stack/teststack/a1b2c3d4-5678-90ab-cdef-EXAMPLE11111)

- AWS::StackName: Devuelve el nombre de la pila, tal y como se especifica en el comando aws cloudformation create-stack (comoteststack)

- AWS::AccountId: Devuelve el ID de cuenta AWS de la cuenta en la que se está creando la pila (como123456789012)

En resumen, los mapeos añaden más flexibilidad a sus plantillas.

PILAS ANIDADAS

Las pilas anidadas son pilas que se crean como parte de otras pilas. Puede crear una pila anidada en otra pila utilizando el recurso AWS::CloudFormation::Stack. Sí, ¡puede declarar una pila dentro de otra pila! 

Quizá se pregunte por qué puede anidar pilas. Imagine que tiene una configuración de balanceador de carga que utiliza para la mayoría de sus pilas. En lugar de copiar y pegar las mismas configuraciones en sus plantillas, puede crear una plantilla dedicada para el equilibrador de carga. Entonces, sólo necesitará utilizar el recurso para referenciar esa plantilla desde otras plantillas. 

Mediante el uso de pilas anidadas, puede modularizar el contenido reutilizable para las pilas de CloudFormation. Incluso podría crear un repositorio de pilas de autoservicio para que lo utilice el equipo de automatización. 

Las pilas anidadas se vuelven más útiles cuando empieza a necesitar gestionar varias pilas con varios entornos y varios equipos de infraestructura.

![image](https://github.com/JuanParraIV/CloudFormation/assets/10248108/baf21c35-4ae9-48f7-aeb7-cfcce83d53ee)

 En resumen, las pilas anidadas son una buena forma de gestionar entornos grandes que abarcan varias pilas. 

Para obtener más información, consulte la [documentación de AWS CloudFormation](https://docs.aws.amazon.com/cloudformation/)


