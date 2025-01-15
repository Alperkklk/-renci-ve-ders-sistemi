from abc import ABC, abstractmethod
import json

# Base Class and Interface
class IPerson(ABC):
    @abstractmethod
    def bilgi_goster(self):
        pass

class Kisi(IPerson):
    def __init__(self, ad, soyad):
        self.ad = ad
        self.soyad = soyad

    def bilgi_goster(self):
        return f"Ad: {self.ad}, Soyad: {self.soyad}"

# Derived Classes
class Ogrenci(Kisi):
    def __init__(self, ad, soyad, ogrenci_no):
        super().__init__(ad, soyad)
        self.ogrenci_no = ogrenci_no

    def bilgi_goster(self):
        return f"{super().bilgi_goster()}, Ogrenci No: {self.ogrenci_no}"

class OgretimGorevlisi(Kisi):
    def __init__(self, ad, soyad, unvan):
        super().__init__(ad, soyad)
        self.unvan = unvan

    def bilgi_goster(self):
        return f"{super().bilgi_goster()}, Unvan: {self.unvan}"

# Course Class
class Ders:
    def __init__(self, ad, kredi, ogretim_gorevlisi):
        self.ad = ad
        self.kredi = kredi
        self.ogretim_gorevlisi = ogretim_gorevlisi
        self.ogrenciler = []

    def ogrenci_ekle(self, ogrenci):
        self.ogrenciler.append(ogrenci)

    def bilgi_goster(self):
        ogrenciler_bilgisi = "\n".join([ogr.bilgi_goster() for ogr in self.ogrenciler])
        return (f"Ders Adı: {self.ad}\nKredi: {self.kredi}\n"
                f"Ogretim Görevlisi: {self.ogretim_gorevlisi.bilgi_goster()}\n"
                f"KayĿtlĿ Öğrenciler:\n{ogrenciler_bilgisi}")

# JSON Handling
class LocalDosyaYoneticisi:
    @staticmethod
    def kaydet(dosya_adi, veri):
        with open(dosya_adi, 'w', encoding='utf-8') as dosya:
            json.dump(veri, dosya, ensure_ascii=False, indent=4)

    @staticmethod
    def yukle(dosya_adi):
        try:
            with open(dosya_adi, 'r', encoding='utf-8') as dosya:
                return json.load(dosya)
        except FileNotFoundError:
            return []

# Example Usage
def main():
    # Create instances
    ogr1 = Ogrenci("Ali", "Kaya", 123)
    ogr2 = Ogrenci("Ayşe", "Demir", 456)
    ogretim_gorevlisi = OgretimGorevlisi("Dr.", "Ahmet", "Yardımcı Doçent")

    # Create course
    ders = Ders("Matematik", 4, ogretim_gorevlisi)
    ders.ogrenci_ekle(ogr1)
    ders.ogrenci_ekle(ogr2)

    # Display information
    print(ders.bilgi_goster())

    # Save to JSON
    ogrenciler = [ogr1.__dict__, ogr2.__dict__]
    ogretim_gorevlileri = [ogretim_gorevlisi.__dict__]
    dersler = [{
        "ad": ders.ad,
        "kredi": ders.kredi,
        "ogretim_gorevlisi": ogretim_gorevlisi.__dict__,
        "ogrenciler": ogrenciler
    }]

    LocalDosyaYoneticisi.kaydet("ogrenciler.json", ogrenciler)
    LocalDosyaYoneticisi.kaydet("ogretim_gorevlileri.json", ogretim_gorevlileri)
    LocalDosyaYoneticisi.kaydet("dersler.json", dersler)

    # Load from JSON
    print("\nJSON'dan Yükleniyor:")
    yuklenen_dersler = LocalDosyaYoneticisi.yukle("dersler.json")
    print(json.dumps(yuklenen_dersler, ensure_ascii=False, indent=4))

if __name__ == "__main__":
    main()
