 ROS2 TurtleBot3 Goal Controller

Bu proje, Yıldız Rover Destek Ekip Ödev 4 kapsamında ROS2 Humble kullanılarak hazırlanmıştır. Projede TurtleBot3 robotu Gazebo simülasyon ortamında çalıştırılmış ve Python ile yazılan bir ROS2 node'u sayesinde robotun belirlenen bir hedef noktaya gitmesi sağlanmıştır.

## Proje Amacı

Bu çalışmada ROS2'nin temel yapısını öğrenmek, topic mantığını kullanmak ve bir robotu simülasyon ortamında kontrol etmek amaçlanmıştır. TurtleBot3 robotu `/odom` topicinden konum bilgisini almakta ve `/cmd_vel` topicine hız komutu gönderilerek hedef noktaya yönlendirilmektedir.

## Kullanılan Sistem ve Araçlar

- Ubuntu 22.04.5 LTS
- ROS2 Humble
- Gazebo
- TurtleBot3 Burger
- Python
- rclpy
- geometry_msgs
- nav_msgs

## Kurulum ve Ortam Hazırlığı

Öncelikle ROS2 Humble kurulumu yapıldı ve ortam değişkenleri `.bashrc` dosyasına eklendi.

```bash
source /opt/ros/humble/setup.bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc

TurtleBot3 modeli olarak bilgisayar performansı nedeniyle burger modeli seçildi.

echo "export TURTLEBOT3_MODEL=burger" >> ~/.bashrc
source ~/.bashrc

Model kontrolü:

echo $TURTLEBOT3_MODEL

Çıktı:

burger
Gazebo Ortamının Açılması

Gazebo arayüzü ilk denemelerde turtlebot3_world.launch.py ile çalıştırıldığında grafik arayüz tarafında hata verdiği için daha hafif ve engelsiz ortam olan empty_world.launch.py kullanıldı. Ödevde engelsiz bir ortam istenildiğinden bu ortam tercih edildi.

Gazebo server arka planda çalıştırıldı:

ros2 launch turtlebot3_gazebo empty_world.launch.py gui:=false

Daha sonra Gazebo arayüzü ayrı olarak açıldı:

gzclient

Bu şekilde TurtleBot3 Burger modeli Gazebo ortamında başarıyla görüntülendi.

ROS2 Topic Kontrolü

Çalışan topicleri görmek için:

ros2 topic list

Önemli topicler:

/cmd_vel
/odom
/scan
/tf

/odom topicinden robotun konum bilgisi izlendi:

ros2 topic echo /odom
Python Node Yapısı

Proje için turtlebot3_goal_controller isimli bir ROS2 Python paketi oluşturuldu.

mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python turtlebot3_goal_controller --dependencies rclpy geometry_msgs nav_msgs

Node dosyası:

turtlebot3_goal_controller/turtlebot3_goal_controller/go_to_goal.py

Bu node:

/odom topicinden robotun anlık konumunu okur.
Hedef nokta ile robotun mevcut konumu arasındaki mesafeyi hesaplar.
Robotun hedefe yönelmesi için açısal hız üretir.
Robotu ileri hareket ettirmek için /cmd_vel topicine hız komutu gönderir.
Hedefe ulaşıldığında robotu durdurur.
Seçilen Hedef Nokta

Başlangıç noktası ile aynı doğrultuda olmaması için hedef nokta hem x hem y ekseninde farklı seçildi:

target_x = 1.5
target_y = 0.8

Bu hedef noktaya ulaşmak için robot önce hedef yönüne dönmekte, ardından hedefe doğru ilerlemektedir.

Projeyi Build Etme

Workspace ana dizinine geçildi:

cd ~/ros2_ws

Paket build edildi:

colcon build

Build işleminden sonra workspace source edildi:

source install/setup.bash
Node'u Çalıştırma

Gazebo açıkken node şu komutla çalıştırıldı:

ros2 run turtlebot3_goal_controller go_to_goal

Çalışma sırasında terminalde robotun anlık konumu, hedefe olan uzaklığı ve açı hatası yazdırıldı.

Örnek çıktı:

Go to goal node started.
Target point: x=1.5, y=0.8
x=0.83, y=0.42, distance=0.77, angle_error=0.00
x=1.42, y=0.76, distance=0.09, angle_error=0.00
Target reached. Robot stopped.
Çıktılar ve Kanıtlar

Proje çıktıları odev_kanit klasöründe tutulmuştur.

odev_kanit/
├── ubuntu_kurulum.png
├── ros2_topic_list.png
├── odom_topic_output.png
├── gazebo_burger_emptyworld.png
├── go_to_goal_package_create.png
├── go_to_goal_start_progress.png
├── target_reached_terminal.png
└── target_movement_video.webm

Bu dosyalar:

Ubuntu 22.04 kurulumunu,
ROS2 topiclerinin çalıştığını,
/odom verisinin alındığını,
Gazebo ortamında TurtleBot3 robotunun açıldığını,
Python node'un çalıştığını,
Robotun hedef noktaya ulaştığını,
Robot hareketinin video kaydını göstermektedir.
Karşılaşılan Sorun

Gazebo ilk olarak turtlebot3_world.launch.py komutu ile açılmaya çalışıldığında gzclient tarafında grafik arayüz hatası oluştu. Robot server tarafında spawn olmasına rağmen görsel arayüz kapanıyordu. Bu nedenle daha hafif ve engelsiz bir ortam olan empty_world.launch.py kullanıldı. Ayrıca Gazebo arayüzü ayrı olarak gzclient komutu ile başlatıldı.

Bu yöntemle TurtleBot3 Burger modeli Gazebo ortamında başarılı şekilde görüntülendi ve Python node ile kontrol edildi.

Hazırlayan

Hilal Yaren Varol
