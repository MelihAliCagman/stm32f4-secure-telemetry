# Adım 0.1: Repo ve klasör yapısı

## Git nedir, neden en baştan düzenli repo?
Git, projenin geçmişini anlık "fotoğraflar" (commit) olarak saklar. Commit geçmişi nasıl
çalıştığımı gösterir, bu yüzden repoyu en baştan düzenli kurdum.

## add, commit, push farkı
- `git add`: dosyaları sepete (staging area) koyar, henüz kayıt yoktur.
- `git commit`: sepeti bilgisayarımdaki geçmişe kaydeder. Push yapılmazsa GitHub'da görünmez.
- `git push`: commit'leri GitHub'a gönderir.

## .gitkeep neden var?
Git boş klasörleri takip etmez. İçine boş bir `.gitkeep` dosyası koyunca klasör repoya girer.

## .gitignore'un görevi
`secrets.h` satırı git'e "bu dosyayı görmezden gel" der. `git status` onu listelemez, `git add .`
sepete koymaz, yani commit ve push'a giremez. Satır olmasaydı PSK Public repoda herkese
açık olurdu. Silsem bile geçmiş commit'te kalırdı. Sırlar asla git'e girmez.

## Yaptığım hata
`git config user.mail` yazdım (doğrusu `user.email`). Git hata mesajı vermedi, sadece boş
çıktı döndü ve komut satırındaki kırmızı daire hata kodunu gösterdi. Komutu yazdığımla
verilenle karşılaştırınca yazım hatasını buldum.

## Diğer notlar
- Terminalde Türkçe karakterler bozuk görünebilir (`Ali ├ça┼čman`). Bu sadece görüntü
  kodlaması, git etkilenmez. Ama derleyici araçları Türkçe karakterli yolda sorun çıkarabilir.
- `LF will be replaced by CRLF` uyarısı Windows satır sonu farkıdır, zararsız.
