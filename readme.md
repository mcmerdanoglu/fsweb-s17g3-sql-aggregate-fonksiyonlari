# SQL Sorgu Alıştırmaları

Bu hafta SQL sorguları üzerine çalışıyorsunuz. Bugünkü alıştırmada sizin için hazırladığımız veritabanında aşağıda istediğimiz sonuçları elde etmenize yarayan SQL sorgularını oluşturacaksınız.

## Proje Kurulumu

Projeyi forklayın ve clonlayın. Tamamladığınızda da pushlayın.

### Kütüphane Bilgi Sistemi

Bu veritabanı, bir okulun kütüphanesinden öğrencilerin aldıkları kitapların bilgisini barındırmaktadır.

#### Tablolar

`ogrenci` tablosu öğrencilerin listesini tutar.
`islem` tablosu öğrencilerin kütüphaneden aldıkları kitapların bilgilerini tutar
`kitap` tablosu kütüphanedeki kitapların bilgisini tutar
`yazar` tablosu kitapların yazarları bilgisini tutar
`tur` tablosu kitapların türlerini tutar.

Tablo ilişiklerini görmek için [ktphn.png] dosyasına göz atın.

Yazdığınız sorguları buradan test edebilirsiniz: [https://ergineer.com/assets/materials/fkg36so5-kutuphanebilgisistemi-sql/]

##### Görevler

Aşağıda istenilen sonuçlara ulaşabilmek için gerekli SQL sorgularını yazın.

MIN-MAX, COUNT-AVG-SUM, GROUP BY, JOINS (INNER, OUTER, LEFT, RIGHT
#ilk 3 soruyu join kullanmadan yazın. 1) Öğrencinin adını, soyadını ve kitap aldığı tarihleri listeleyin.
SELECT
(SELECT ograd FROM ogrenci WHERE ogrno = islem.ogrno) AS ogr_adi,
(SELECT ogrsoyad FROM ogrenci WHERE ogrno = islem.ogrno) AS ogr_soyadi,
atarih AS alinan_tarih
FROM
islem;

    2) Fıkra ve hikaye türündeki kitapların adını ve türünü listeleyin.
         SELECT
    k.kitapadi AS kitap_adi,
    t.turadi AS tur_adi
    FROM
    tur t, kitap k
    WHERE
    k.turno = t.turno AND t.turadi= 'Fıkra' OR t.turadi= 'Hikaye'
    ORDER BY t.turadi, k.kitapadi;

    3) 10B veya 10C sınıfındaki öğrencilerin numarasını, adını, soyadını ve okuduğu kitapları listeleyin.

    3-A)

SELECT
(SELECT kitapadi FROM kitap WHERE kitapno= islem.kitapno) AS okunan_kitap,
ogrno, ograd, ogrsoyad
FROM ogrenci
WHERE ogrno = islem.ogrno AND sinif ='10B' OR sinif='10C';

     3-B)

SELECT
(SELECT kitapadi FROM kitap WHERE kitapno = (
SELECT kitapno FROM islem WHERE ogrno = ogrenci.ogrno
)) AS okunan_kitap,
ogrno, ograd, ogrsoyad
FROM
ogrenci
WHERE
sinif = '10B' OR sinif = '10C';

    #join ile yazın
    4) Öğrencinin adını, soyadını ve kitap aldığı tarihleri listeleyin.
    SELECT ograd, ogrsoyad, islem.atarih
    FROM ogrenci AS ogr
    INNER JOIN islem
    ON ogr.ogrno= islem.ogrno;

    5) Fıkra ve hikaye türündeki kitapların adını ve türünü listeleyin.
    SELECT kitapadi, turadi FROM kitap AS ktp
    INNER JOIN tur AS konu
    ON ktp.turno = konu.turno
    WHERE (konu.turadi = 'Fıkra' OR konu.turadi = 'Hikaye')
    ORDER BY konu.turadi;

    6) 10B veya 10C sınıfındaki öğrencilerin numarasını, adını, soyadını ve okuduğu kitapları, öğrenci adına göre listeleyin.
    SELECT
    ogr.ogrno,
    ogr.ograd,
    ogr.ogrsoyad,
    kitap.kitapadi AS okunan_kitap
    FROM
    ogrenci AS ogr
    INNER JOIN islem
    ON ogr.ogrno = islem.ogrno
    INNER JOIN kitap
    ON kitap.kitapno = islem.kitapno
    WHERE
    (ogr.sinif = '10B' OR ogr.sinif = '10C')
    ORDER BY ogr.ograd;

    7) Kitap alan öğrencinin adı, soyadı, kitap aldığı tarih listelensin. Kitap almayan öğrencilerinde listede görünsün.
    	SELECT ograd, ogrsoyad, islem.atarih
    	FROM ogrenci AS ogr
    	LEFT JOIN islem
    	ON ogr.ogrno= islem.ogrno;

    8) Kitap almayan öğrencileri listeleyin.
    	SELECT ograd, ogrsoyad
        FROM ogrenci AS ogr
        LEFT JOIN islem
        ON ogr.ogrno= islem.ogrno
        WHERE islem.atarih IS NULL;

    9) Alınan kitapların kitap numarasını, adını ve kaç defa alındığını kitap numaralarına göre artan sırada listeleyiniz.
    	SELECT ktp.kitapno, ktp.kitapadi, COUNT(islem.atarih) as toplam_alinma
    FROM kitap AS ktp
    INNER JOIN islem
    ON ktp.kitapno = islem.kitapno
    GROUP BY ktp.kitapno, ktp.kitapadi
    ORDER BY ktp.kitapno ASC;

    10) Alınan kitapların kitap numarasını, adını kaç defa alındığını (alınmayan kitapların yanında 0 olsun) listeleyin.
    SELECT ktp.kitapno, ktp.kitapadi, COUNT(islem.atarih) as toplam_alinma
    FROM kitap AS ktp
    LEFT JOIN islem
    ON ktp.kitapno = islem.kitapno
    GROUP BY ktp.kitapno, ktp.kitapadi
    ORDER BY ktp.kitapno ASC;

    11) Öğrencilerin adı soyadı ve aldıkları kitabın adı listelensin.
    SELECT ogr.ograd, ogr.ogrsoyad, ktp.kitapadi
    FROM ogrenci AS ogr
    INNER JOIN islem
    ON ogr.ogrno = islem.ogrno
    INNER JOIN kitap AS ktp
    ON islem.kitapno = ktp.kitapno;

    12) Her öğrencinin adı, soyadı, kitabın adı, yazarın adı soyad ve kitabın türünü ve kitabın alındığı tarihi listeleyiniz. Kitap almayan öğrenciler de listede görünsün.
    SELECT ogr.ograd, ogr.ogrsoyad, ktp.kitapadi, yaz.yazarad, yaz.yazarsoyad, turadi, islem.atarih
    FROM ogrenci AS ogr
    LEFT JOIN islem
    ON ogr.ogrno = islem.ogrno
    LEFT JOIN kitap AS ktp
    ON islem.kitapno = ktp.kitapno
    LEFT JOIN yazar AS yaz
    ON yaz.yazarno = ktp.kitapno
    LEFT JOIN tur
    ON tur.turno = ktp.turno;

    13) 10A veya 10B sınıfındaki öğrencilerin adı soyadı ve okuduğu kitap sayısını getirin.
    SELECT CONCAT (ogr.ograd, ogr.ogrsoyad) AS 'AD Soyad', ogr.sinif, COUNT(islem.atarih) AS Okuma
    FROM ogrenci AS ogr
    INNER JOIN islem
    ON ogr.ogrno = islem.ogrno
    WHERE sinif IN ('10A', '10B')
    GROUP BY CONCAT (ogr.ograd, ogr.ogrsoyad), ogr.sinif;

    14) Tüm kitapların ortalama sayfa sayısını bulunuz.
    #AVG
      SELECT AVG(sayfasayisi) FROM kitap;

    15) Sayfa sayısı ortalama sayfanın üzerindeki kitapları listeleyin.
    	SELECT *
    	FROM kitap
    	WHERE sayfasayisi > (SELECT AVG(sayfasayisi) FROM kitap)
    	ORDER BY sayfasayisi DESC;

    16) Öğrenci tablosundaki öğrenci sayısını gösterin
    		SELECT COUNT(ogrno)
    		FROM ogrenci;

    17) Öğrenci tablosundaki toplam öğrenci sayısını toplam sayı takma(alias kullanımı) adı ile listeleyin.
    		SELECT COUNT(ograd) AS 'Toplam Sayı'
    		FROM ogrenci;

    18) Öğrenci tablosunda kaç farklı isimde öğrenci olduğunu listeleyiniz.
    	SELECT DISTINCT ograd
    	FROM ogrenci;

    19) En fazla sayfa sayısı olan kitabın sayfa sayısını listeleyiniz.
    	SELECT sayfasayisi  FROM kitap
    	ORDER BY sayfasayisi DESC
    	LIMIT 1;

    20) En fazla sayfası olan kitabın adını ve sayfa sayısını listeleyiniz.
    	SELECT kitapadi, sayfasayisi  FROM kitap
    	ORDER BY sayfasayisi DESC
    	LIMIT 1;

    21) En az sayfa sayısı olan kitabın sayfa sayısını listeleyiniz.
    	SELECT sayfasayisi  FROM kitap
    	ORDER BY sayfasayisi ASC
    	LIMIT 1;

    22) En az sayfası olan kitabın adını ve sayfa sayısını listeleyiniz.
    	SELECT kitapadi, sayfasayisi  FROM kitap
    	ORDER BY sayfasayisi ASC
    	LIMIT 1;

    23) Dram türündeki en fazla sayfası olan kitabın sayfa sayısını bulunuz.
    	SELECT MAX(sayfasayisi) As 'Dram Türündeki en fazla sayfalı kitap' FROM kitap
    	INNER JOIN tur
    	ON kitap.turno = tur.turno
    	WHERE turadi = 'Dram';

    24) numarası 15 olan öğrencinin okuduğu toplam sayfa sayısını bulunuz.
    	SELECT SUM(sayfasayisi) AS '15 nolu ogrnin okuduğu toplam sayfa sayisi' from kitap
    	INNER JOIN islem
    	ON kitap.kitapno = islem.kitapno
    	INNER JOIN ogrenci
    	ON islem.ogrno = ogrenci.ogrno
    	WHERE ogrenci.ogrno = 15;

    25) İsme göre öğrenci sayılarının adedini bulunuz.(Örn: ali 5 tane, ahmet 8 tane )
    	SELECT ograd, COUNT(ograd) from ogrenci
    	GROUP BY ograd;

    26) Her sınıftaki öğrenci sayısını bulunuz.
    	SELECT COUNT(ograd) AS 'Ogrenci Sayisi', sinif FROM ogrenci
    	GROUP BY sinif
    	ORDER BY sinif DESC;

    27) Her sınıftaki erkek ve kız öğrenci sayısını bulunuz.
    	SELECT COUNT(ograd) AS 'Ogrenci Sayisi', sinif, cinsiyet FROM ogrenci
    	GROUP BY sinif, cinsiyet
    	ORDER BY sinif DESC;

    28) Her öğrencinin adını, soyadını ve okuduğu toplam sayfa sayısını büyükten küçüğe doğru listeleyiniz.
    	SELECT CONCAT(ogrenci.ograd, ogrenci.ogrsoyad) AS 'Ad Soyad', SUM(sayfasayisi) AS	'Okunan toplam sayfa sayisi' from kitap
    	INNER JOIN islem
    	ON kitap.kitapno = islem.kitapno
    	INNER JOIN ogrenci
    	ON islem.ogrno = ogrenci.ogrno
    	GROUP BY ogrenci.ograd, ogrenci.ogrsoyad
    	ORDER BY SUM(sayfasayisi) DESC;


    29) Her öğrencinin okuduğu kitap sayısını getiriniz.
    	SELECT CONCAT(ogrenci.ograd, ogrenci.ogrsoyad) AS 'Ad Soyad', COUNT(kitap.kitapno) AS 'Okunan kitap sayisi' from kitap
    	INNER JOIN islem
    	ON kitap.kitapno = islem.kitapno
    	INNER JOIN ogrenci
    	ON islem.ogrno = ogrenci.ogrno
    	GROUP BY ogrenci.ograd, ogrenci.ogrsoyad
    	ORDER BY COUNT(kitap.kitapno) DESC;
