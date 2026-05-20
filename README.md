ROS2 TurtleBot3 Goal Controller

Bu proje, Rover Destek Ekip Ödev 4 kapsamında ROS2’ye giriş ve TurtleBot3 simülasyon uygulaması için hazırlanmıştır. Projede Ubuntu 22.04 üzerinde ROS2 Humble kurulmuş, TurtleBot3 robotu Gazebo ortamında çalıştırılmış ve Python ile yazılan bir ROS2 node'u kullanılarak robotun belirlenen hedef noktaya gitmesi sağlanmıştır.

Proje Amacı

Bu çalışmanın amacı ROS2 temel yapısını öğrenmek, topic mantığını kullanmak ve bir robotu simülasyon ortamında kontrol etmektir.
Robotun konum bilgisi `/odom` topicinden okunmuştur. Robotun hareket etmesi için `/cmd_vel` topicine hız komutları gönderilmiştir. Python node'u robotun mevcut konumu ile hedef nokta arasındaki mesafeyi ve açı farkını hesaplayarak robotu hedefe yönlendirmiştir.

Kullanılan Sistem ve Araçlar

Ubuntu 22.04.5 LTS
ROS2 Humble
Gazebo
TurtleBot3 Burger
Python
rclpy
geometry_msgs
nav_msgs

Kurulum Özeti

ROS2 Humble kurulumu yapıldıktan sonra ROS2 ortamı terminale tanıtılmıştır:
```bash
source /opt/ros/humble/setup.bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
TurtleBot3 modeli olarak bilgisayar performansı daha stabil olduğu için `burger` modeli seçilmiştir:
```bash
echo "export TURTLEBOT3_MODEL=burger" >> ~/.bashrc
source ~/.bashrc
```
Model kontrolü:
```bash
echo $TURTLEBOT3_MODEL
```
Beklenen çıktı:
```bash
burger
```
Gazebo Ortamının Açılması

İlk denemelerde `turtlebot3_world.launch.py` kullanıldığında Gazebo görsel arayüzü olan `gzclient` tarafında hata oluştu. Robot server tarafında spawn olmasına rağmen arayüz kapanıyordu. Bu nedenle daha hafif ve engelsiz bir ortam olan `empty_world.launch.py` kullanıldı.
Ödevde robotun engelsiz bir ortamda hareket etmesi istendiği için `empty_world` ortamı uygun görülmüştür.

Gazebo server arka planda şu komutla çalıştırıldı:
```bash
ros2 launch turtlebot3_gazebo empty_world.launch.py gui:=false
```
Daha sonra Gazebo arayüzü ayrı bir terminalde açıldı:
```bash
gzclient
```
Bu yöntemle TurtleBot3 Burger modeli Gazebo ortamında başarılı şekilde görüntülendi.


ROS2 Topic Kontrolü

Gazebo çalıştıktan sonra aktif topicler kontrol edildi:
```bash
ros2 topic list
```

Önemli topicler:
```bash
/cmd_vel
/odom
/scan
/tf
```

Robotun odometry verisini görmek için:
```bash
ros2 topic echo /odom
```

Bu komutla robotun anlık konumu ve yönelimi takip edilmiştir.


Python Package Oluşturma
Proje için `turtlebot3_goal_controller` isimli bir ROS2 Python paketi oluşturuldu:
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python turtlebot3_goal_controller --dependencies rclpy geometry_msgs nav_msgs
```
Node dosyası şu konumdadır:
```text
turtlebot3_goal_controller/turtlebot3_goal_controller/go_to_goal.py
```
Python Node'un Çalışma Mantığı

`go_to_goal.py` dosyasında yazılan node şu işlemleri yapmaktadır:
`/odom` topicinden robotun anlık konumunu okur.
Robotun mevcut konumu ile hedef nokta arasındaki mesafeyi hesaplar.
Robotun hedefe dönmesi için açı hatasını hesaplar.
`/cmd_vel` topicine linear ve angular hız komutları gönderir.
Robot hedefe yeterince yaklaştığında hızları sıfırlayıp robotu durdurur.

Seçilen Hedef Nokta

Başlangıç noktası ile aynı doğrultuda olmaması için hedef nokta hem x hem de y ekseninde farklı seçilmiştir:
```python
target_x = 1.5
target_y = 0.8
```
Bu sayede robot yalnızca düz bir çizgide ilerlemek yerine hedef noktanın yönüne göre dönerek hareket etmiştir.

Projeyi Build Etme

Workspace ana dizinine geçilir:
```bash
cd ~/ros2_ws
```
Paket build edilir:
```bash
colcon build
```
Build işleminden sonra workspace source edilir:
```bash
source install/setup.bash
```
Node'u Çalıştırma
Gazebo ortamı açıkken Python node şu komutla çalıştırılır:
```bash
ros2 run turtlebot3_goal_controller go_to_goal
```
Node çalıştığında terminalde robotun konumu, hedefe olan uzaklığı ve açı hatası yazdırılır.
Örnek çıktı:
```bash
Go to goal node started.
Target point: x=1.5, y=0.8
x=0.83, y=0.42, distance=0.77, angle_error=0.00
x=1.42, y=0.76, distance=0.09, angle_error=0.00
Target reached. Robot stopped.
```
Bu çıktı robotun hedef noktaya ulaştığını ve durduğunu göstermektedir.

Quick Run
Projeyi çalıştırmak için önce ROS2 ortamı source edilir:
```bash
source /opt/ros/humble/setup.bash
```
TurtleBot3 modeli ayarlanır:
```bash
export TURTLEBOT3_MODEL=burger
```
Gazebo server bir terminalde başlatılır:
```bash
ros2 launch turtlebot3_gazebo empty_world.launch.py gui:=false
```
Başka bir terminalde Gazebo arayüzü açılır:
```bash
gzclient
```
Başka bir terminalde proje build edilir ve node çalıştırılır:
```bash
cd ~/ros2_ws
colcon build
source install/setup.bash
ros2 run turtlebot3_goal_controller go_to_goal
```
Çıktılar ve Kanıtlar

Proje çıktıları `outputs` klasöründe tutulmuştur.
```text
outputs/
├── ubuntu_kurulum.png
├── ros2_topic_list.png
├── odom_topic_output.png
├── gazebo_burger_emptyworld.png
├── go_to_goal_package_create.png
├── go_to_goal_start_progress.png
├── target_reached_terminal.png
└── target_movement_video.webm
```
Bu dosyalar şunları göstermektedir:

Ubuntu 22.04 kurulumunun yapıldığını,
ROS2 topiclerinin oluştuğunu,
`/odom` verisinin alındığını,
Gazebo ortamında TurtleBot3 robotunun açıldığını,
Python node'un oluşturulup çalıştırıldığını,
Robotun hedef noktaya ulaştığını,
Robot hareketinin video kaydını.

Karşılaşılan Sorun ve Çözüm

Gazebo ilk olarak `turtlebot3_world.launch.py` ile çalıştırıldığında görsel arayüz tarafında hata oluştu. Terminal çıktılarında robotun spawn olduğu, `/cmd_vel` topicine abone olduğu ve `/odom` topicini yayınladığı görülmesine rağmen `gzclient` kapanıyordu.
Bu nedenle daha hafif ve engelsiz bir ortam olan `empty_world.launch.py` kullanıldı. Gazebo server `gui:=false` parametresi ile başlatıldı ve görsel arayüz ayrı olarak `gzclient` komutu ile açıldı.
Bu çözümle TurtleBot3 Burger modeli Gazebo ortamında başarılı şekilde çalıştırıldı.

Sonuç

Bu projede ROS2 Humble kurulumu yapılmış, TurtleBot3 robotu Gazebo ortamında çalıştırılmış ve Python ile yazılan bir ROS2 node'u sayesinde robotun belirlenen hedef noktaya gitmesi sağlanmıştır. Robot `/odom` topicinden aldığı konum bilgisine göre hedefe yönelmiş ve `/cmd_vel` topicine gönderilen hız komutları ile hareket etmiştir. Hedefe ulaştığında robot durdurulmuştur.

Hazırlayan

Hilal Yaren Varol
