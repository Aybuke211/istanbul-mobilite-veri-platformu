# 0001 Mimari: batch, streaming ve tablo formatı

Durum: Kabul edildi
Tarih: 23 Kasım 2026

## Bağlam

İBB'nin toplu ulaşım ve trafik kaynakları saatlik ölçümler içerir; analitik tüketicilerin hem geçmiş dönemleri tutarlı biçimde sorgulaması hem de yeni gelen trafik olaylarını kısa sürede görebilmesi gerekir. Ocak 2024 lab verisi toplu ulaşımda 17.283.355, trafikte 957.453 satır içerir; dönemsel yükler büyük hacimli dosyaların yeniden işlenmesini de gerektirir. Akış için hedef gecikme, olayın platforma alınmasından sorgulanabilir olmasına kadar beş dakikanın altıdır; bu hedef kaynağın yayın sıklığını değiştirmez.

## Karar

Tarihsel ve dönemsel yükler Airflow ile batch olarak, canlı olaylar Kafka ile streaming olarak işlenecek; lakehouse tabloları Apache Iceberg biçiminde tutulup Polaris REST kataloğunda yönetilecek ve akışta platforma alımdan sorgulanabilirliğe kadar beş dakikanın altında gecikme hedeflenecektir.

## Gerekçe

Batch akışı, 17 milyondan fazla satır içeren aylık toplu ulaşım dosyalarının yeniden çalıştırılmasını ve geçmiş verilerin toplu yüklenmesini sağlar. Kafka akışı, canlı olaylar için beş dakikanın altındaki hedefi destekler; Iceberg snapshot'ları yazımları izlenebilir kılar ve şema değişikliklerine olanak tanır. Polaris, farklı işlem motorlarının aynı tablo kataloğunu kullanmasını sağlar.

Elenen seçenekler: Yalnızca batch kullanmak canlı akış için belirlenen gecikme hedefini karşılamaz; analitik tabloları düz CSV dosyaları olarak tutmak snapshot ve tablo düzeyinde şema yönetimi sağlamaz.

## Sonuçlar

Airflow, Kafka ve Polaris servislerinin işletilmesi ve izlenmesi gerekir; Iceberg yazımlarında küçük dosyaların birleştirilmesi ve snapshot saklama politikası ayrıca yönetilir. Akış gecikmesi kaynak verinin yayımlanma zamanını kapsamaz ve saatlik kaynaklar nedeniyle son kullanıcıya görünen veri daha eski olabilir. Batch ile streaming yazımları aynı tabloya yöneliyorsa yinelenen olayları önlemek için anahtar ve tekrar işleme kuralları tanımlanmalıdır.
