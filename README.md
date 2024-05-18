<h1 align="center"> 0rbit </h1>

> ÖNEMLİ: Repo'yu gerçekleştirirken [BURADAKİ](https://www.youtube.com/watch?v=pFS4zYWxzNA) müziği dinlemezseniz rewards alamıyorsunuz.

> Selamlar, Arweave ve AO'yu Tuna Tavus gibi yemeye deveam ediyoruz xd

> Bu repo'da tıpkı AO'da yaptığımız gibi 0rbit için 2 bot kurup Puanları toplayacağız.

> zaten öncelerinden aşinasınız siz hemen yaparsınız. Bu 2 görev puanı 200 ve 300 totalde 500 puan

> Donanım olarak herhangi bir sunucunuzda kurulum gerçekleştirebilirsiniz, bir koşul yok şu an.

<h1 align="center"> Kurulum </h1>

```console
# sunucu güncelleme
sudo apt update -y && sudo apt upgrade -y

# komutları sırasıyla girelim:
curl -sL https://deb.nodesource.com/setup_20.x -o /tmp/nodesource_setup.sh
sudo bash /tmp/nodesource_setup.sh
sudo apt install nodejs

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc
nvm install v20.10.0
nvm use v20.10.0
npm install -g npm@latest

# AOyu yüklüyoruz
npm i -g https://get_ao.g8way.io
```

> Şimdi tam burada Visual Studio Code'a geçiyoruz (yoksa indirelim)

> [Buradaki](https://github.com/ruesandora/AO/blob/main/chatroom.md) repo'da User / Root kısmına kadar yapmamız yeterli.

<h1 align="center"> Parallel Everene Giriş #1 - Price Botu </h1>

```console
# sunucumuzda çalıtırıyoruz
aos orbit --cron 30-seconds
# proeses IDimizi kaydediyoruz.
```

> Aşağıya yazacağım kodu girmeden önce Visual'dan new file diyoruz.

> File ismi: `0rbit-Price-Feed.lua` olacak ve aşağıdaki kodu içine girip ctrl + s ile kaydediyoruz

> Kodda 2 yer düzenlencek, `PID XXXXXXXX` yazan yere orbit process id - `Handlers.add("GITHUB",` yazan yere github account yazıyoruz.

```console
-- PID XXXXXXXXXXXXXXXX

local json = require("json")

_ORBIT = "WSXUI2JjYUldJ7CKq9wE1MGwXs-ldzlUlHOQszwQe0s"

function handleError(msg, errorMessage)
    ao.send({
        Target = msg.From,
        Tags = {
            Action = "Error",
            ["Message-Id"] = msg.Id,
            Error = errorMessage
        }
    })
end

Handlers.add("GITHUB",
    Handlers.utils.hasMatchingTag("Action", "Sponsored-Get-Request"),
    function(msg)
        local token = msg.Tags.Token
        if not token then
            handleError(msg, "Token not provided")
            return
        end
        
        local url = "https://api.coingecko.com/api/v3/simple/price?ids=" .. token .. "&vs_currencies=usd"
        ao.send({
            Target = _ORBIT,
            Action = "Get-Real-Data",
            Url = url
        })
        print("Pricefetch request sent for " .. token)
    end
)

Handlers.add("ReceiveData",
    Handlers.utils.hasMatchingTag("Action", "Receive-Response"),
    function(msg)
        print("Received data: " .. msg.Data)
        local res = json.decode(msg.Data)
        local token = msg.Tags.Token
        if res[token] and res[token].usd then
            ao.send({
                Target = msg.From,
                Tags = {
                    Action = "Price-Response",
                    ["Message-Id"] = msg.Id,
                    Price = res[token].usd
                }
            })
            print("Price of " .. token .. " is " .. res[token].usd)
        else
            handleError(msg, "Failed to fetch price")
        end
    end
)
```

> Sunucunmuza geri dönüp:

```console
# dosyayı load ediyoruz
.load 0rbit-Price-Feed.lua

# monitoru çalıştırıyoruz
.monitor

# eth fiyatını çekiyoruz
Send({ Target = ao.id, Action="Sponsored-Get-Request", Tags = { Token = "ethereum" }})
```

> [Buradakine](https://github.com/0rbit-co/quest/pull/348/commits/acb31a5d8249c16014a789c188d50b7ccf4a32ec) benzer bir çıktınız olacak bunun fotoğrafını alın kaydedin.

> [Buradaki](https://github.com/0rbit-co/quest) repoyu forkluyoruz - forkumuzda 1 klasör açıyoruz, anlatıyorum:

> Aşağıdaki gibi File'a ismi veriyoruz (kendi github ısmınızı girin) sonra / işareti koyunca o bir klasör oluyor

<img width="652" alt="Ekran Resmi 2024-05-18 12 19 40" src="https://github.com/ruesandora/0rbit/assets/101149671/db965b8e-f63f-4a8d-b1b4-3e875bcc4326">

> Klasör içindeki ilk dosyamızın adı `0rbit-Price-Feed.lua` onun içine Visual'daki dosyayı import ediyoruz.

> Klasör içindeki ikinci dosyamızın adı `0rbit-Price-Feed.jpg` veya `0rbit-Price-Feed.png` (türü neyse) 

> Price bot için 2 bilgi olan .lua ve görseli hazırladık ve kaydedelim fork reponuzu.

```console
# Şimdi Claim edelim yaptığımız işi
Send({Target= "O3SXXYqQCNTbBedJjsW6wkPnrKFZq8DPLkKjO7zhztE", Action = "Claim", Quest = "Price-Bot", User = "GITHUB"})
# Github adını düzenleyin en sonda komudun.
```

![image](https://github.com/ruesandora/0rbit/assets/101149671/81a52b3b-be8e-4dfd-a782-e1f90544a9f4)


<h1 align="center"> Parallel Everene Giriş #2 - News Botu </h1>














