Actually i didnt make too much, just stay on cosmic setup. Not any special terminal or other DE. Just cosmic DE. 

## Here what i did in this setup:

* I just removed dock/Taskbar.
* Seperate the panel and make transparent about %50.
* [Wallpaper](wallpaper.png). but originally I took from [here](https://github.com/sid-kailasa/hyprland-dots/blob/main/wallpapers/wall2.png). (That is also good hyprland rice but i just wanna experience the cosmic de.)
* Add some applets from Cosmic App Store : 
    * **Logo Menu** and then choose special logo, i chose [berserk logo](/logos/Berserk-Logo-white.svg) you can choose whatever do u want. it should be .svg file u can convert any image files through web-sites
    * **Sysinfo**; for see CPU, RAM usage and bitrates.
    * **Weather**, for see the weather... i guess?
    * And add **Numbered Workspaces** from panel settings.
* I changed colors from settings, go themes and change the color here is the Hex codes :
    #8A9BE8  and for the darker tone : #151624 
    (You can also import display settings from [here](ice-frost.ron). Just download .rov file and import from settings.)

**That's it. Just ask me if you have a questions. Thank you for your interest!**

# Screenshots

![Desktop](/screenshots/screenshot1.png)
![Desktop & Terminal](/screenshots/screenshot2.png)
![IDE & Terminal](/screenshots/screenshot3.png)
![Tiling](/screenshots/tiling-screenshot.png)

> Thats my first rice attempt. I just wanna stay clean, You can take from here and make it better.


# OPTİMİZASYON İÇİN YAPTIKLARIM 

Bu rapor, Dell G15 5530 gibi hibrit (Intel + NVIDIA) ekran kartlı dizüstü bilgisayarlarda Pop!_OS deneyimini en üst seviyeye çıkarmak için uyguladığımız teknik adımları içerir.

Amacımız; günlük işlerde düşük güç tüketen Intel kartını kullanırken, oyun veya ağır işlerde NVIDIA kartını tek bir komutla (`prime-run`) devreye sokmak ve bu süreci otomatize etmektir. İşte sistemine uyguladığımız tüm iyileştirmelerin kapsamlı dokümantasyonu:

---

# 🚀 Pop!_OS Hibrit GPU ve Performans Yapılandırma Raporu

## 📋 Genel Bakış

Bu yapılandırma ile sistemine şunları kazandırdık:

1. **Dinamik GPU Yönetimi:** Sistem genel olarak tasarruf modunda çalışırken, sadece istediğimiz uygulamalar NVIDIA ile başlar.
2. **Gelişmiş Terminal Kısayolları (Alias):** Karmaşık terminal komutlarını tek kelimelik takma adlara indirdik.
3. **Performans Otomasyonu:** Ağır bir uygulama (örneğin Prism Launcher) açıldığında sistemin otomatik olarak "Performans" moduna geçmesini sağladık.

---

## 🛠 Adım Adım Uygulanan İşlemler

### 1. Donanımsal Temel: Hibrit Modun Aktifleştirilmesi

Pop!_OS'in tüm bu özellikleri kullanabilmesi için sistemin "Hybrid" modda olması şarttır. Bu mod, Intel kartını görüntü için kullanırken NVIDIA kartını sadece ihtiyaç anında (Offload) uyandırır.

* **Komut:** `system76-power graphics hybrid`
* **Açıklama:** Bu komut sistemi hibrit moda sokar. (Değişikliklerin tam uygulanması için bilgisayarı yeniden başlatmak gerekebilir).

### 2. Teşhis Araçlarının Kurulması

Sistemin ekran kartını doğru tanıyıp tanımadığını kontrol etmek için gerekli kütüphaneyi yükledik.

* **Komut:** `sudo apt update && sudo apt install mesa-utils -y`
* **Açıklama:** `glxinfo` komutunu kullanmamızı sağlar. Bu sayede o an hangi GPU'nun aktif olduğunu terminalden görebiliriz.

### 3. `.bashrc` Yapılandırması (Alias Sistemi)

Linux terminalinin hafızası olan `.bashrc` dosyasını düzenleyerek özel komutlarımızı tanımladık. Bu dosya, terminal her açıldığında otomatik olarak yüklenir.

**Uygulanan Kod Bloğu:**
Terminale `nano ~/.bashrc` yazarak dosyanın en altına şu satırları ekledik:

```bash
# --- ÖZEL GPU VE PERFORMANS YAPILANDIRMASI ---

# Prime-run: NVIDIA kartını manuel olarak tetikleyen ana komut
alias prime-run='__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia'

# Güç Profilleri: Şifre girmeden veya menülerle uğraşmadan mod değiştirme
alias profile-pil='system76-power profile battery'
alias profile-performance='system76-power profile performance'
alias profile-balanced='system76-power profile balanced'

# GPU Durum Kontrolü: NVIDIA kartı o an çalışıyor mu yoksa uykuda mı?
alias gpu='cat /proc/driver/nvidia/gpus/*/power'

# Otomasyon Örneği (Prism Launcher): 
# Önce sistemi performans moduna alır, sonra launcher'ı NVIDIA kartıyla başlatır.
alias mc='system76-power profile performance && prime-run /opt/prismlauncher/PrismLauncher'

```

### 4. Yapılandırmayı Aktif Etme

Dosyayı kaydettikten sonra terminalin bu yeni komutları tanıması için şu komutu çalıştırdık:

* **Komut:** `source ~/.bashrc`
* **Açıklama:** Terminali kapatıp açmaya gerek kalmadan değişiklikleri o anki oturuma uygular.

---

## 📊 Kullanım Rehberi ve Doğrulama

Sistemini kullanırken aşağıdaki tabloda yer alan kısa komutları kullanabilirsin:

| Komut | Ne İşe Yarar? | Ne Zaman Kullanmalı? |
| --- | --- | --- |
| `gpu` | NVIDIA kartının güç durumunu gösterir. | Kartın uykuda (Off) olup olmadığını kontrol ederken. |
| `prime-run [uygulama]` | Belirtilen uygulamayı NVIDIA ile açar. | `prime-run steam` gibi; oyun başlatırken. |
| `mc` | Minecraft (Prism) özel otomasyonunu çalıştırır. | Oyuna girmeden önce tek tuşla performans + GPU için. |
| `profile-pil` | Sistemi düşük güç tüketimine alır. | Şarjdayken değil, bataryadayken kullanım için. |

### Test Etme (Her Şey Yolunda mı?)

Sisteminin NVIDIA kartını başarıyla kullanıp kullanmadığını şu testle doğrulayabilirsin:
`prime-run glxinfo | grep "OpenGL renderer"`

> **Sonuç:** Eğer ekranda `NVIDIA GeForce RTX 4050` (veya kendi modelin) yazıyorsa, kurduğumuz `prime-run` sistemi kusursuz çalışıyor demektir.

---

**Rapor Notu:** Bu yapılandırma, sistem kaynaklarını en verimli şekilde kullanmanı sağlar. Eğer ileride yeni bir oyun veya ağır bir yazılım (Blender, PyTorch vb.) eklersen, onu da `prime-run [program_adı]` şeklinde çalıştırabilirsin.
