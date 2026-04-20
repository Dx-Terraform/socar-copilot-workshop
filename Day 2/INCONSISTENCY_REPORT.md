# Day 2 Workshop — Tutarsızlık Raporu

## Döküman Hakkında

Bu rapor, `Day 2/` klasöründeki workshop dökümanlarının (`00-giris.md` – `10-kapanis.md`)
çapraz analizinden elde edilmiştir. Hem insan okuyucular hem de yapay zeka ajanları tarafından
hata tespiti amacıyla kullanılmak üzere yapılandırılmıştır.

**Genel Güvenilirlik Notu:** İlk dökümanlar (`01-faz0.md` – `04-faz3.md`) büyük ölçüde doğrudur.
Hatalar ağırlıklı olarak sonraki dökümanlardan (`05-faz4.md` ve sonrası) kaynaklanmaktadır.
Bir tutarsızlık tespit edildiğinde, ilk dökümanın verdiği bilgiyi referans alın.

---

## Temel Kavramsal Kurallar (Referans)

Tespit edilen hataların büyük kısmı aşağıdaki iki kurala dayanmaktadır. Bu kurallar
`02-faz1.md` ve `03-faz2.md` dökümanlarında doğru şekilde açıklanmıştır.

### Kural 1 — Prompt Node Çıktıları Record Tipidir

"Create a prompt" (Prompt node) çıktısı Copilot Studio'da otomatik olarak **Record** tipi
bir değişken oluşturur. Bu değişken doğrudan bir metin (`String`) girdisi bekleyen herhangi
bir alanda kullanılamaz.

- ❌ Yanlış kullanım (Send a message içinde): `{x} → MyPromptOutput`
- ✅ Doğru kullanım: `{x} → MyPromptOutput.text`
- ✅ Doğru kullanım (bir başka prompt'un Text tipli girdisinde): `MyPromptOutput.text`

### Kural 2 — String Tipi Değişkenlerde `.text` Kullanılmaz

"Set a variable value" node'u ile `PromptOutput.text` değeri atanarak oluşturulan değişkenler
**String** tipindedir. String değişkenlerde `.text` alt özelliği yoktur.

- ❌ Yanlış: `MyStringVariable.text`
- ✅ Doğru: `MyStringVariable`

### Değişken Tipi — Hızlı Başvuru Tablosu

| Değişken           | Oluşturulduğu Yer                          | Tipi   | Mesajda Kullanım      |
| ------------------ | ------------------------------------------ | ------ | --------------------- |
| `Specfile`         | Ask a question (File)                      | File   | Prompt input (doc)    |
| `SartnameSummary`  | Prompt node çıktısı (Faz 2)               | Record | `.text` gerekir       |
| `CriteriaDraft`    | Prompt node çıktısı (Faz 3)               | Record | `.text` gerekir       |
| `CriteriaFinal`    | Set variable ← CriteriaDraft.text (Faz 3) | String | Doğrudan kullanılır   |
| `CriteriaRevisionInput` | Ask a question (User's entire response) | String | Doğrudan kullanılır  |
| `RevisedCriteriaFinal` | Prompt node çıktısı (Faz 4)           | Record | `.text` gerekir       |
| `CriteriaTable`    | Prompt node çıktısı (Faz 5)               | Record | `.text` gerekir       |
| `AHPSummary`       | Prompt node çıktısı (Faz 7)               | Record | `.text` gerekir       |
| `q1winner`…`q3score` | Adaptive card çıktısı (Faz 5)          | String | Doğrudan kullanılır   |
| `CriteriaResult`…`BestScore` | Flow (Respond to agent) çıktısı | String | Doğrudan kullanılır |

---

## Tutarsızlık Kataloğu

Her madde aşağıdaki alanları içerir:

- **ID** — Benzersiz tanımlayıcı (`KATEOGRI-NUMARA` formatı)
- **Dosya / Bölüm** — Hatanın bulunduğu dosya ve challenge
- **Tür** — Hata kategorisi
- **Hatalı İçerik** — Dökümanın ne söylediği
- **Doğru İçerik** — Ne olması gerektiği
- **Kısa Açıklama** — Neden hata olduğunun gerekçesi

---

### KATEGORİ A — `.text` Uzantısı ve Değişken Tipi Hataları

---

#### A-1

| Alan             | Değer                                                                    |
| ---------------- | ------------------------------------------------------------------------ |
| **Dosya**        | `04-faz3.md`                                                             |
| **Bölüm**        | Challenge 3.1, Adım 9                                                    |
| **Tür**          | Eksik `.text` uzantısı — adım talimatı ile beklenen çıktı çelişkisi     |
| **Hatalı Adım**  | "Insert variable butonuna tıklayıp `CriteriaDraft` değişkenini seçin."  |
| **Beklenen Çıktı (doğru)** | "Son Send a message node'unda `CriteriaDraft.text` değişkeni mesaja eklenmiş durumdadır." |
| **Doğru Talimat** | Insert variable → `CriteriaDraft.text` seçin.                          |

**Açıklama:** `CriteriaDraft`, bir Prompt node çıktısıdır ve Record tipidir. Send a message
node'unda metin olarak göstermek için `.text` alt özelliğinin seçilmesi zorunludur. Adım 9
talimatı yanlışlıkla sadece `CriteriaDraft` demektedir; dökümanın kendi beklenen çıktı bölümü
ise doğru olarak `CriteriaDraft.text` demektedir. **Referans: beklenen çıktı doğrudur, adım
metni hatalıdır.**

---

#### A-2

| Alan              | Değer                                                                         |
| ----------------- | ----------------------------------------------------------------------------- |
| **Dosya**         | `05-faz4.md`                                                                  |
| **Bölüm**         | Challenge 4.1, Adım 7 — Prompt node input bağlantısı                         |
| **Tür**           | Eksik `.text` uzantısı — Record tipi değişken Text girdisine doğrudan bağlanmış |
| **Hatalı İçerik** | `criteriadraft` → `CriteriaDraft`                                            |
| **Doğru İçerik**  | `criteriadraft` → `CriteriaDraft.text`                                       |

**Açıklama:** Adım 5'te `{criteriadraft}` için oluşturulan prompt girdisi **Text** tipidir.
`CriteriaDraft` ise bir Prompt node çıktısı olduğundan **Record** tipindedir. Text tipi bir
prompt girdisine Record tipi değişken doğrudan bağlanamaz; `.text` alt özelliği seçilmelidir.

---

#### A-3

| Alan              | Değer                                                                          |
| ----------------- | ------------------------------------------------------------------------------ |
| **Dosya**         | `05-faz4.md`                                                                   |
| **Bölüm**         | Challenge 4.2, Beklenen Çıktı                                                 |
| **Tür**           | Yanlış `.text` uzantısı — String değişkene `.text` eklenmesi                 |
| **Hatalı İçerik** | "Son Send a message node'unda `CriteriaFinal.text` mesaja eklenmiş durumdadır." |
| **Doğru İçerik**  | "Son Send a message node'unda `CriteriaFinal` mesaja eklenmiş durumdadır."   |

**Açıklama:** `CriteriaFinal`, Faz 3 Challenge 3.2 Adım 4'te "Set a variable" node'u ile
`CriteriaDraft.text` değeri atanarak oluşturulmuştur. Bu değer bir String olduğundan
`CriteriaFinal` **String tipindedir**. String değişkenlerin `.text` alt özelliği yoktur;
dökümanın Adım 2 talimatı (sadece `CriteriaFinal` der) doğrudur, beklenen çıktı hatalıdır.
**Referans: Adım 2 doğrudur, beklenen çıktı hatalıdır.**

---

#### A-4 *(En Kritik Hata)*

| Alan              | Değer                                                                             |
| ----------------- | --------------------------------------------------------------------------------- |
| **Dosya**         | `07-faz5.md`                                                                      |
| **Bölüm**         | Challenge 5.2, Adım 7 ve Beklenen Çıktı                                          |
| **Tür**           | Yanlış değişken adı + yanlış `.text` kullanımı — çift hata                      |
| **Hatalı Adım 7** | "Insert variable → `CriteriaTable` ekleyin"  *(değişken adı doğru ama .text eksik)* |
| **Hatalı Beklenen Çıktı** | "Send a message node'unda anket açıklaması ve `CriteriaFinal.text` değişkeni eklenmiştir." |
| **Doğru İçerik**  | Adım 7: `CriteriaTable.text` seçin. Beklenen Çıktı: `CriteriaTable.text` değişkeni eklenmiştir. |

**Açıklama:** İki ayrı hata içerir:
1. Beklenen çıktı yanlış bir değişken adı (`CriteriaFinal`) kullanmaktadır; gösterilen içerik
   challenge'ın az önce ürettiği kriter tablosudur ve doğru değişken `CriteriaTable`'dır.
2. Adım 7 talimatında değişken adı doğru (`CriteriaTable`) ancak `.text` uzantısı eksiktir.
   `CriteriaTable` bir Prompt node çıktısıdır ve Record tipindedir; mesajda kullanmak için
   `CriteriaTable.text` gerekir.
3. Ayrıca: beklenen çıktıda `CriteriaFinal.text` yazması da ayrıca yanlıştır, zira `CriteriaFinal`
   String tipindedir ve `.text` alt özelliği yoktur.

---

### KATEGORİ B — UI Gezinme ve Node Adı Tutarsızlıkları

---

#### B-1

| Alan              | Değer                                                                        |
| ----------------- | ---------------------------------------------------------------------------- |
| **Dosyalar**      | `03-faz2.md` (Faz 2) ↔ `07-faz5.md` (Faz 5)                               |
| **Bölümler**      | Challenge 2.1 Adım 2 ↔ Challenge 5.1 Adım 2                                |
| **Tür**           | Aynı butonun farklı konumda gösterilmesi                                    |
| **Faz 2 (doğru)** | "Sağ üstteki **+ Add a topic** butonuna tıklayın"                          |
| **Faz 5 (hatalı)** | "Solda bulunan **Add a topic** butonuna tıklayın"                          |

**Açıklama:** "Add a topic" butonu Copilot Studio arayüzünde Topics sekmesinde sağ üstte
yer almaktadır. Faz 2'nin tarifi arayüzle örtüşmekte; Faz 5'in "sol tarafta" tarifi
hatalıdır. **Referans: Faz 2 doğrudur.**

---

#### B-2

| Alan              | Değer                                                                       |
| ----------------- | --------------------------------------------------------------------------- |
| **Dosyalar**      | `03-faz2.md` (Faz 2) ↔ `07-faz5.md` (Faz 5)                              |
| **Bölümler**      | Challenge 2.1 Adım 2 ↔ Challenge 5.1 Adım 2                               |
| **Tür**           | Aynı seçenek için farklı menü etiketi kullanımı                            |
| **Faz 2 (doğru)** | "**Blank** seçeneğini seçin"                                               |
| **Faz 5 (hatalı)** | "**From blank** seçin"                                                    |

**Açıklama:** Copilot Studio'da boş topic oluşturma seçeneğinin etiketi "Blank"tir ("From blank"
değil). Faz 2 doğru etiket adını kullanmaktadır. **Referans: Faz 2 doğrudur.**

---

#### B-3

| Alan               | Değer                                                                         |
| ------------------ | ----------------------------------------------------------------------------- |
| **Dosyalar**       | `04-faz3.md` (Faz 3) ↔ `05-faz4.md` (Faz 4)                                |
| **Bölümler**       | Challenge 3.2 Adım 4 ↔ Challenge 4.2 Adım 1                                 |
| **Tür**            | Aynı node için farklı ad ve farklı büyük/küçük harf kullanımı               |
| **Faz 3 yazımı**   | "**Variable Management** → **Set a variable**"                               |
| **Faz 4 yazımı**   | "**Variable management** altındaki **Set a variable value**"                 |
| **Doğru node adı** | "Set a variable value"                                                       |

**Açıklama:** Copilot Studio'da doğru node adı "Set a variable value"dır. Faz 3'te kısaltılmış
ve hatalı isimlendirilmiş ("Set a variable"), Faz 4'te doğru ad kullanılmıştır. Başlık
büyük/küçük harf tutarsızlığı da mevcuttur ("Variable Management" vs "Variable management").
**Referans: Faz 4 doğrudur.**

---

#### B-4

| Alan              | Değer                                                                           |
| ----------------- | ------------------------------------------------------------------------------- |
| **Dosya**         | `09-faz7.md`                                                                    |
| **Bölüm**         | Challenge 7.2, Adım 6                                                           |
| **Tür**           | Yanlış node terminolojisi                                                       |
| **Hatalı İçerik** | "Action node'u canvas'a geldiğinde tüm input değişkenlerinin listeden seçili olduğunu … doğrulayın." |
| **Doğru İçerik**  | "Create a prompt node'u canvas'a geldiğinde …"                                 |

**Açıklama:** Challenge 7.2 Adım 1'de "add a tool → new prompt" ile eklenen node, tüm
döküman boyunca "Create a prompt" veya "Prompt node" olarak adlandırılmaktadır. "Action node"
terminolojisi gerçekte flow/tool bağlantı node'larına karşılık gelir ve bu bağlamda kullanımı
yanlıştır.

---

### KATEGORİ C — Mantıksal / Yapısal Tutarsızlıklar

---

#### C-1

| Alan              | Değer                                                                            |
| ----------------- | -------------------------------------------------------------------------------- |
| **Dosya**         | `04-faz3.md`                                                                     |
| **Bölüm**         | Challenge 3.2, Adım 3 ve üstündeki açıklama notu                               |
| **Tür**           | Adım talimatı ile açıklama notu birbiriyle çelişiyor                           |
| **Not (doğru)**   | "Koşulların değeri (`is equal to`) varsayılan olarak doğru gelir, değiştirmenize gerek yoktur." |
| **Adım 3 (çelişen)** | "Birinci dal için condition dropdown'ından **`Uygun, devam et`** seçin."    |

**Açıklama:** Açıklama notu koşulların Copilot Studio tarafından otomatik ve doğru şekilde
doldurulduğunu; herhangi bir değişiklik gerekmediğini söylemektedir. Ancak hemen ardından
gelen Adım 3, kullanıcıdan açıkça dropdown'dan seçim yapmasını istemektedir. Bu iki yönerge
çelişmektedir. **Referans: Açıklama notu doğrudur; Adım 3 bu koşulların yalnızca
*doğrulanması* gerektiğini ifade edecek şekilde yazılmalıdır.**

---

#### C-2

| Alan              | Değer                                                                       |
| ----------------- | --------------------------------------------------------------------------- |
| **Dosya**         | `07-faz5.md`                                                                |
| **Bölüm**         | Faz 5 challenge numaralandırması                                            |
| **Tür**           | Eksik challenge numarası                                                    |
| **Hatalı Sıra**   | Challenge 5.1 → Challenge 5.2 → Challenge 5.4 *(5.3 yok)*                 |
| **Doğru Sıra**    | 5.1 → 5.2 → 5.3 → 5.4 olmalı ya da 5.4 yerine 5.3 yazılmalıdır           |

**Açıklama:** Döküman, Challenge 5.2'den doğrudan 5.4'e atlamaktadır. Challenge 5.3 ya kaldırıldı
ve başlıklar güncellenmedi, ya da numaralandırma hatası yapıldı. Kafa karışıklığı yaratır;
katılımcılar 5.3'ü atlayıp atlamadıklarını merak eder.

---

#### C-3

| Alan              | Değer                                                                              |
| ----------------- | ---------------------------------------------------------------------------------- |
| **Dosya**         | `08-faz6.md`                                                                       |
| **Bölüm**         | Challenge 6.5, Adım 5 — "Respond to agent" node çıktıları                        |
| **Tür**           | Tek bir output alanına birden fazla değer atanması — mantıksal hata             |
| **Hatalı İçerik** | "Output 1 — Name: `Criteria Results` — Value: ⚡ → `teknikWeight`, `operasyonelWeight`, `escWeight` değerlerini seçin." |
| **Doğru İçerik**  | Her ağırlık değeri için ayrı output oluşturulmalı **ya da** Power Automate formülüyle (`concat` vb.) tek bir string'e birleştirilmeli; hangi yöntem seçilirse seçilsin açıkça belirtilmeli. |

**Açıklama:** Power Automate'in "Respond to agent" node'unda her output alanı yalnızca **tek**
bir dinamik değer kabul eder. Talimat `teknikWeight`, `operasyonelWeight` ve `escWeight`
değerlerini aynı anda tek output'a seçmeyi ima etmektedir; bu mümkün değildir. Seçenek (a)
üç ayrı output oluşturulur ve Faz 7'de ayrı değişkenlere alınır; ya da seçenek (b) bir
birleştirme formülü (`concat(string(teknikWeight),' | ',string(operasyonelWeight),' | ',string(escWeight))`) kullanılır. Mevcut talimat her iki yaklaşımı da açıklamamaktadır.

---

#### C-4

| Alan              | Değer                                                                             |
| ----------------- | --------------------------------------------------------------------------------- |
| **Dosya**         | `09-faz7.md`                                                                      |
| **Bölüm**         | Challenge 7.3, Adım 4 ve Adım 7                                                  |
| **Tür**           | "Go to another topic" sonrasına "End all topics" eklenmesi — olası redundant akış |
| **Hatalı Adımlar** | Adım 4: "+ → Topic management → End all topics" (Go to another topic'ten hemen sonra) |
|                   | Adım 7: Aynı yapı ikinci dal için tekrarlanıyor                                  |
| **Doğru Yaklaşım** | "Go to another topic → `Anket ve Hesaplama`" sonrasında "End all topics" gerekmez; akış o topic'e geçer. "Anket ve Hesaplama" topic'i zaten Challenge 7.2 Adım 9'da "End current topic" ile kapatılmaktadır. |

**Açıklama:** "Go to another topic" node'u kontrolü hedef topic'e devreder. Bu node'un hemen
ardına "End all topics" eklemek, hedef topic çalışmadan önceki davranışı belirsizleştirir veya
akışı istemeden sonlandırabilir. Referans olarak Challenge 7.2 Adım 9 incelenmeli: Anket ve
Hesaplama topic'i kendi içinde zaten "End current topic" ile kapanmaktadır; ana topic'te
ek bir sonlandırıcıya gerek yoktur.

---

## Özet Tablosu

| ID  | Dosya          | Bölüm                    | Hata Türü                                      | Kritiklik |
| --- | -------------- | ------------------------ | ---------------------------------------------- | --------- |
| A-1 | `04-faz3.md`   | Challenge 3.1, Adım 9   | Eksik `.text` — adım ile beklenen çıktı çelişkisi | Orta   |
| A-2 | `05-faz4.md`   | Challenge 4.1, Adım 7   | Record tipi değişken Text girdisine `.text`siz bağlandı | Yüksek |
| A-3 | `05-faz4.md`   | Challenge 4.2, Beklenen Çıktı | String değişkene yanlış `.text` eklendi   | Orta      |
| A-4 | `07-faz5.md`   | Challenge 5.2, Adım 7 + Beklenen Çıktı | Yanlış değişken adı + yanlış `.text` — çift hata | Yüksek |
| B-1 | `07-faz5.md`   | Challenge 5.1, Adım 2   | Add a topic butonunun konumu yanlış tarif edildi | Düşük  |
| B-2 | `07-faz5.md`   | Challenge 5.1, Adım 2   | Blank yerine "From blank" menü etiketi         | Düşük     |
| B-3 | `04-faz3.md` + `05-faz4.md` | Ch. 3.2 Adım 4 / Ch. 4.2 Adım 1 | Set a variable node adı farklı yazıldı | Düşük |
| B-4 | `09-faz7.md`   | Challenge 7.2, Adım 6   | Prompt node "Action node" olarak adlandırıldı  | Düşük     |
| C-1 | `04-faz3.md`   | Challenge 3.2, Adım 3   | Adım talimatı ile açıklama notu çelişiyor     | Orta      |
| C-2 | `07-faz5.md`   | Faz 5 genel             | Challenge 5.3 eksik / numaralandırma atlaması  | Düşük     |
| C-3 | `08-faz6.md`   | Challenge 6.5, Adım 5   | Tek output'a birden fazla değer atanması       | Yüksek    |
| C-4 | `09-faz7.md`   | Challenge 7.3, Adım 4/7 | Go to another topic + End all topics çakışması | Orta      |

---

## AI Ajan İçin Yapılandırılmış Hata Listesi

```json
[
  {
    "id": "A-1",
    "file": "04-faz3.md",
    "section": "Challenge 3.1 - Adım 9",
    "type": "missing_dot_text",
    "variable": "CriteriaDraft",
    "variable_type": "Record",
    "incorrect": "Insert variable → CriteriaDraft",
    "correct": "Insert variable → CriteriaDraft.text",
    "note": "Beklenen çıktı zaten doğru (CriteriaDraft.text); adım metni hatalı.",
    "severity": "medium"
  },
  {
    "id": "A-2",
    "file": "05-faz4.md",
    "section": "Challenge 4.1 - Adım 7 - Prompt input binding",
    "type": "missing_dot_text",
    "variable": "CriteriaDraft",
    "variable_type": "Record",
    "incorrect": "criteriadraft → CriteriaDraft",
    "correct": "criteriadraft → CriteriaDraft.text",
    "note": "Prompt girdisi Text tipi; Record tipi değişken .text olmadan bağlanamaz.",
    "severity": "high"
  },
  {
    "id": "A-3",
    "file": "05-faz4.md",
    "section": "Challenge 4.2 - Beklenen Çıktı",
    "type": "erroneous_dot_text",
    "variable": "CriteriaFinal",
    "variable_type": "String",
    "incorrect": "CriteriaFinal.text",
    "correct": "CriteriaFinal",
    "note": "CriteriaFinal String tipindedir (.text alt özelliği yoktur). Adım 2 talimatı doğru.",
    "severity": "medium"
  },
  {
    "id": "A-4",
    "file": "07-faz5.md",
    "section": "Challenge 5.2 - Adım 7 + Beklenen Çıktı",
    "type": "wrong_variable_and_missing_dot_text",
    "variable_in_step": "CriteriaTable",
    "variable_in_expected_output": "CriteriaFinal",
    "correct_variable": "CriteriaTable",
    "correct_usage": "CriteriaTable.text",
    "note": "Beklenen çıktı yanlış değişken adı (CriteriaFinal) kullanıyor. CriteriaTable Record tipindedir; .text gerekir. CriteriaFinal ise String tipidir ve .text eki almaz zaten yanlış değişkedir.",
    "severity": "high"
  },
  {
    "id": "B-1",
    "file": "07-faz5.md",
    "section": "Challenge 5.1 - Adım 2",
    "type": "ui_location_mismatch",
    "incorrect": "Solda bulunan Add a topic",
    "correct": "Sağ üstteki + Add a topic",
    "reference": "03-faz2.md Challenge 2.1 Adım 2",
    "severity": "low"
  },
  {
    "id": "B-2",
    "file": "07-faz5.md",
    "section": "Challenge 5.1 - Adım 2",
    "type": "ui_label_mismatch",
    "incorrect": "From blank",
    "correct": "Blank",
    "reference": "03-faz2.md Challenge 2.1 Adım 2",
    "severity": "low"
  },
  {
    "id": "B-3",
    "files": ["04-faz3.md", "05-faz4.md"],
    "sections": ["Challenge 3.2 Adım 4", "Challenge 4.2 Adım 1"],
    "type": "node_name_mismatch",
    "incorrect": "Variable Management → Set a variable",
    "correct": "Variable management → Set a variable value",
    "severity": "low"
  },
  {
    "id": "B-4",
    "file": "09-faz7.md",
    "section": "Challenge 7.2 - Adım 6",
    "type": "wrong_node_terminology",
    "incorrect": "Action node",
    "correct": "Create a prompt node",
    "severity": "low"
  },
  {
    "id": "C-1",
    "file": "04-faz3.md",
    "section": "Challenge 3.2 - Adım 3 ve üstündeki not",
    "type": "self_contradiction",
    "note_says": "Koşullar otomatik dolu gelir, değiştirmeye gerek yok",
    "step_says": "Dropdown'dan seçim yap",
    "resolution": "Koşullar otomatik gelir; Adım 3 yalnızca doğrulama amacıyla okunmalıdır.",
    "severity": "medium"
  },
  {
    "id": "C-2",
    "file": "07-faz5.md",
    "section": "Faz 5 genel",
    "type": "numbering_gap",
    "sequence_found": ["5.1", "5.2", "5.4"],
    "expected": ["5.1", "5.2", "5.3", "5.4"],
    "severity": "low"
  },
  {
    "id": "C-3",
    "file": "08-faz6.md",
    "section": "Challenge 6.5 - Adım 5 - Output 1",
    "type": "multiple_values_single_output",
    "incorrect": "Criteria Results output'una teknikWeight + operasyonelWeight + escWeight birlikte seç",
    "correct": "Her değer için ayrı output VEYA concat formülü ile tek stringe birleştir",
    "severity": "high"
  },
  {
    "id": "C-4",
    "file": "09-faz7.md",
    "section": "Challenge 7.3 - Adım 4 ve 7",
    "type": "redundant_conflicting_node",
    "incorrect": "Go to another topic ardından End all topics",
    "correct": "Go to another topic yeterlidir; Anket ve Hesaplama zaten End current topic ile kapanır.",
    "severity": "medium"
  }
]
```
