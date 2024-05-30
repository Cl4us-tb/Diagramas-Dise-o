# Diagramas-Dise-o
Codigo de los diagramas hechos en Structurizr
workspace "Name" "Description" {

    model {
    admin = person "ADMINISTRADOR" "administrador del sistema"
    cliente = person "CLIENTE" "cliente del aplicativo, pasajero"
    conductor = person "CONDUCTOR" "conductor del corredor"
    app = softwareSystem "CorredorConectado" "sistema de mejora para la experiencia de usuario de los corredores de transporte" {
        webapp = container "WebbApp" "applicacion movil de usuarios" "Flutter.dart"
        
        apigw = container "APIGW" "apigateway" "Java Rest"{
            Enruta_sol = component "Enrutador de Solicitudes" "Dirige las solicitudes entrantes a los servicios correspondientes en el backend del sistema" "Spring Cloud Gateway"
            Autenticador = component "Autenticador y Autorizador" "Autentica y autoriza las solicitudes entrantes antes de acceder a los servicios del sistema" "Spring Security"
            Gestor_Solicitud = component "Gestor de Solicitudes Externas" "Gestiona las solicitudes externas entrantes y las comunica con los servicios internos del sistema" "Spring Boot"
            Integra_Visa = component "Integrador con Visa" "Comunica con el sistema de pagos Visa para operaciones de recarga de tarjetas de pago" "Java SDK Visa"
            Integra_MasterCard = component "Integrador con MasterCard" "Comunica con el sistema de pagos MasterCard para operaciones de recarga de tarjetas de pago" "Java SDK MasterCard"
            Integra_Maps = component "Integrador con Google Maps" "Comunica con la API de Google Maps para obtener datos de mapas" "Node.js"


        }
        c1 = container "VENTAS" "Calcula y muestra los precios que los clientes poseen con sus descuentos"{
            calcuD = component "Calculadora de Precios" "Calcula los precios de los servicios con los descuentos aplicados."
        }
        c2 = container "RUTAS" "Calcula el tiempo y muestra las rutas posibles"{
         R1 = component "Componente Rutas" "Maneja las solicitudes relacionadas con rutas" "Spring MVC RestController"
         R2 = component "Servicio de Cálculo de Rutas" "Contiene la lógica de negocio para calcular las rutas" "Spring Bean"
        }
        c3 = container "Perfiles" "permite que el usuario ingrese su información personal y credenciales"{
         P1 = component "Controlador de Perfiles" "Maneja las solicitudes relacionadas con perfiles" "Spring MVC RestController"
         P2 = component "Servicio de Perfiles" "Contiene la lógica de negocio para la gestión de perfiles" "Spring Bean"
        }
        c4 = container "CORREDOR ROJO" "coordinador de buses y de conductores" {
         CR1 = component "Gestor de Horarios" "Administra los horarios de salida de los conductores desde los paraderos"
         CR2 = component "Seguimiento de Ubicación" "Administra las ubicaciones enviadas por los conductores"
        }
        c5 = container "ALERTAS" "Informa al usuario de cambios en las rutas, precios o avisar de alguna emergencia o accidente"{
            AS = component "Sistema de Notificaciones" "Encargado de enviar notificaciones a los usuarios sobre cambios en las rutas, precios o eventos de emergencia."
            cambioRuta = component "Gestor de Cambios de Rutas" "Administra y notifica sobre cambios en las rutas de transporte."
            cambioPrecio = component "Gestor de Precios" "Administra y notifica sobre cambios en los precios de los servicios de transporte."
        }
        
        bd = container "ORACLE" "base de datos" "Oracle 19c port 1521"{
           Rutas = component "Componente Rutas" "Almacena información de las rutas" "Base de Datos"
          Precios = component "Componente Precios" "Almacena todos los precios de todo en el sistema" "Base de Datos"
          Usuarios = component "Componente Usuarios" "Almacena toda la información del usuario" "Base de Datos"
          Horarios = component "Componente Horarios" "Almacena las horas de salida y llegada de los conductores" "Base de Datos"
          Ubicaciones = component "Componente Ubicaciones" "Almacena las ubicaciones de los autobuses" "Base de Datos"
        }
    }
    visa = softwareSystem "Visa" "sistema de pagos"
    mastercard = softwareSystem "Mastercard" "sistema de pagos"
    maps = softwareSystem "Google Maps" "API de google maps"

    // contexto
    
    admin -> app "accede a permisos especiales de la aplicación"
    cliente -> app "utiliza la aplicación para transportarse"
    conductor -> app "utiliza la aplicación para trabajar en su ruta"
    app -> visa "utiliza el método de pago por tarjeta visa para recargar saldo"
    app -> mastercard "utiliza el método de pago por tarjeta mastercard para recargar saldo"
    app -> maps "utiliza la API de google maps para mostrar los mapas"
    
    //contenedores
    
    cliente -> webapp "usa"
    admin -> webapp "usa"
    conductor -> webapp "usa"
    webapp -> apigw "usa"
    apigw -> c1 "usa"
    apigw -> c2 "usa"
    apigw -> c3 "usa"
    apigw -> c4 "usa información de"
    apigw -> c5 "usa información de"
    c1 -> bd "lee y escribe"
    c2 -> bd "lee y escribe"
    c3 -> bd "lee y escribe"
    c4 -> bd "lee y escribe"
    c5 -> bd "lee y escribe"
    apigw -> mastercard "usa"
    apigw -> maps "usa"
    apigw -> visa "usa"
    
    //componente RUTA
     R1 -> R2 "Usa"
     apigw -> R1 "Envía solicitudes HTTP a"
     R2 -> bd "Usa y escribe en"
     
     //componente PERFIL
     apigw -> P1 "Envía solicitudes HTTP a"
     P1 -> P2 "Usa"
     P2 -> bd "Lee y escribe en"
     
      //componente Corredor Rojo
     apigw -> CR1 "Envía y recibe solicitudes de gestión de horarios"
     apigw -> CR2 "Envía y recibe ubicaciones de conductores"
     CR1 -> bd "Lee y escribe en"
     CR2 -> bd "Lee y escribe en"
     
     //componente ALERTAS
     apigw -> AS "Recibe notificaciones"
     AS -> cambioRuta "recibe información sobre cambios en las rutas de transporte"
     AS -> cambioPrecio "recibe información sobre cambios en precios de transporte"
     cambioRuta -> bd "lee los datos de las rutas"
     cambioPrecio -> bd "lee los datos de los precios"
     
     //componente VENTAS
     apigw -> calcuD "Recibe el calculo del precio."
     calcuD -> bd "Accede a la base de datos."
     
     //componentes APIGW
     webapp -> Enruta_Sol "Envia las solicitudes externas"
     webapp ->  Gestor_Solicitud "Recibe solicitudes internas del sistema"
     Enruta_Sol -> Autenticador "Dirige las solicitudes para ser revisadas"
     Autenticador -> Gestor_Solicitud "Valída las solicitudes de acuerdo a los permisos del usuario"
     Gestor_Solicitud -> Integra_Visa "Envía solicitudes de pago y recarga"
     Gestor_Solicitud -> Integra_MasterCard "Envía solicitudes de pago y recarga"
     Gestor_Solicitud -> Integra_Maps "Obtener y mostrar ubicaciones y mapas virtuales"
     Gestor_Solicitud -> c3 "Envía la solicitud de perfil"
     Gestor_Solicitud -> c4 "Envía solicitud de horarios y ubicación"
     Gestor_Solicitud -> c5 "Recibe solicitudes de alerta"
     Gestor_Solicitud -> c1 "Envía y Recibe solicitud de descuento"
     Gestor_Solicitud -> c2 "Envía y Recibe solicitudes de calculo de ruta"
     
     Integra_Visa -> visa "Dirige solicitud"
     Integra_MasterCard -> mastercard  "Dirige solicitud"
     Integra_Maps -> maps  "Dirige solicitud"
     
     //componentes ORACLE
     
     c1 -> Precios "Recibe precios actuales"
     c1 -> Usuarios "Recibe descuentos de usuario"
     c5 -> Precios "Recibe y compara precios actuales"
     c5 -> Rutas "Recibe y compara rutas actuales"
     c3 -> Usuarios "Recibe y actualiza datos del usuario"
     Usuarios -> c2 "Recibe rutas del usuario"
     Rutas -> c2 "Envia informacion de las rutas pre-establecidas"
     c4 -> Horarios "Actualiza hora de salida del conductor"
     c4 -> Ubicaciones "Actualiza ubicación del conductor"

     
    }
    
    
    views {
    systemContext app {
        include *
        autolayout
    }
     container app {
        include *
        autolayout
    }
    component c2 {
            include *
            autolayout
        }
    component c3 {
            include *
            autolayout
        }
    component c4 {
            include *
            autolayout
        }
    component c5 {
            include *
            autolayout
        }
    component c1 {
            include *
            autolayout
        }
    component apigw {
            include *
            autolayout
        }
    component bd {
            include *
            autolayout
        }
    
        theme default
    }
}
