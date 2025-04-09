<div align="center">
<picture>
    <source srcset="https://imgur.com/5bYAzsb.png" media="(prefers-color-scheme: dark)">
    <source srcset="https://imgur.com/Os03JoE.png" media="(prefers-color-scheme: light)">
    <img src="https://imgur.com/Os03JoE.png" alt="Escudo UNAL" width="350px">
</picture>

<h3>Curso de Robótica 2025-I</h3>

<h1>Introducción a Turtlesim</h1>

<h2>Guía 02 - Instalación de Turtlesim en ROS 2 Humble</h2>


<h4>Pedro Fabián Cárdenas Herrera<br>
    Manuel Felipe Carranza Montenegro</h4>

</div>

<div align="justify"> 

## Introducción

El paquete `turtlesim` es un simulador ligero incluido en ROS 2 que permite comprender de forma visual y práctica los conceptos fundamentales del sistema operativo robótico, como nodos, tópicos y servicios. A través de una tortuga animada, es posible interactuar con estos elementos utilizando comandos de terminal o interfaces gráficas como `rqt`.

Este entorno es ideal para quienes están comenzando en ROS 2, ya que proporciona una experiencia amigable para aprender a publicar y suscribirse a tópicos, llamar servicios y controlar nodos, todo sin necesidad de hardware físico.

## Objetivos

- Instalar y ejecutar el simulador `turtlesim`.
- Controlar una tortuga mediante el teclado usando ROS 2.
- Visualizar y manipular nodos, tópicos y servicios mediante comandos y la herramienta gráfica `rqt`.
- Utilizar servicios para crear nuevas tortugas y cambiar propiedades como el color del trazo.
- Aplicar remapeo de tópicos para controlar múltiples tortugas simultáneamente.

> **Prerrequisitos:** 
> - Tener ROS 2 correctamente instalado y configurado (ej. ROS 2 Humble).
> - Haber ejecutado `source /opt/ros/humble/setup.bash` en cada terminal nueva.

## Instalar turtlesim

```bash
sudo apt update
sudo apt install ros-humble-turtlesim
```

Verifica la instalación:

```bash
ros2 pkg executables turtlesim
```

## Iniciar turtlesim

```bash
ros2 run turtlesim turtlesim_node
```

## Controlar la tortuga

En otra terminal:

```bash
ros2 run turtlesim turtle_teleop_key
```

> Usa las flechas del teclado para mover la tortuga y dibujar.

Ver nodos y recursos activos:

```bash
ros2 node list
ros2 topic list
ros2 service list
ros2 action list
```

## Instalar rqt

```bash
sudo apt update
sudo apt install '~nros-humble-rqt*'
```

Ejecutar:

```bash
rqt
```

Si no aparecen los plugins:

```bash
rqt --force-discover
```

## Usar rqt
En el menú superior: `Plugins > Services > Service Caller`

### Usar el servicio `/spawn`

- Escoge `/spawn` en el menú desplegable.
- Asigna nombre (turtle2) y coordenadas (x=1.0, y=1.0).
- Haz clic en Call.
- Aparecerá una nueva tortuga.

### Usar el servicio `/set_pen`

- Selecciona `/turtle1/set_pen`.
- Cambia r=255, g=0, b=0, width=5.
- Haz clic en Call.
- La tortuga dibujará con una línea roja gruesa.

## Controlar turtle2 (remapeo)

Abre otra terminal y ejecuta:

```bash
ros2 run turtlesim turtle_teleop_key --ros-args --remap turtle1/cmd_vel:=turtle2/cmd_vel
```

> Ahora puedes controlar turtle2 desde esta terminal y turtle1 desde la anterior.

## Configurar Visual Studio Code para ROS 2

```bash
sudo apt update
sudo apt install python3-colcon-common-extensions
```

Si trabajas con un workspace (recomendado), carga su entorno, si no tienes un workspace, crea uno:

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
source /opt/ros/humble/setup.bash
```
##  Crear un Nodo en Python para Controlar la Tortuga
Dentro de tu workspace, crea un paquete:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python my_turtle_controller
```

Edita el archivo `setup.py` dentro de `my_turtle_controller` para asegurarte de que tiene:

```python
entry_points={
    'console_scripts': [
        'move_turtle = my_turtle_controller.move_turtle:main',
    ],
},
```

Ahora, en la carpeta `my_turtle_controller/my_turtle_controller/`, crea un archivo llamado `move_turtle.py` y escribe lo siguiente:

```python
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist

class TurtleController(Node):
    def __init__(self):
        super().__init__('turtle_controller')
        self.publisher_ = self.create_publisher(Twist, '/turtle1/cmd_vel', 10)
        self.timer = self.create_timer(0.5, self.move_turtle)

    def move_turtle(self):
        msg = Twist()
        msg.linear.x = 2.0  # Velocidad hacia adelante
        msg.angular.z = 1.0  # Rotación
        self.publisher_.publish(msg)
        self.get_logger().info('Moviendo la tortuga')

def main(args=None):
    rclpy.init(args=args)
    node = TurtleController()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()
```

## Compilar y Ejecutar el Nodo
Guarda los cambios, regresa a la raíz de tu `workspace`, compílalo y ejecútalo:

```bash
cd ~/ros2_ws
colcon build
source install/setup.bash
ros2 run my_turtle_controller move_turtle
```

## Recursos útiles

- [Documentación oficial de ROS 2 Humble](https://docs.ros.org/en/humble/index.html)  
  Guía completa oficial con tutoriales, ejemplos, y API.

- [Documentación Oficial](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Introducing-Turtlesim/Introducing-Turtlesim.html) Docuementación Oficial turtlesim Intslación y Uso.

- [Lista de plugins de rqt](https://wiki.ros.org/rqt)  
  Descripción de los plugins disponibles para `rqt`, útil para visualización y depuración.

- [Extensión de ROS para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-iot.vscode-ros)  
  Herramienta para desarrollar, depurar y visualizar proyectos ROS 2 en VS Code.

- [Repositorio oficial de turtlesim](https://github.com/ros/ros_tutorials/tree/humble/turtlesim)  
  Código fuente del paquete, útil para entender su funcionamiento interno.

</div>