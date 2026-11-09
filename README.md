
ajanlar.json
kopru.json
CLAUDE.local.md

# çalışma verisi: koşu logları, olaylar, durum, çıktılar, hafıza içeriği
olaylar.jsonl
durum/
kosular/
cikti/
hafiza/*
!hafiza/.gitkeep
port
*.log

# sırlar
.env
.env.*

# python / sistem
__pycache__/
*.pyc
.DS_Store
NET_TIPLER = {"net", "islak", "yasam", "oda"}
DOLASIM_TIPLER = {"dolasim", "merdiven", "asansor", "koridor"}


def alan_ozeti(odalar: list[dict], brut_alan: float | None = None) -> dict:
    """odalar: [{"ad": "Salon", "alan": 28.5, "tip": "net"}, ...]
    tip: net | islak | dolasim | ortak | diger"""
    toplam = sum(float(o["alan"]) for o in odalar)
    net = sum(float(o["alan"]) for o in odalar if o.get("tip", "net") in NET_TIPLER)
    dolasim = sum(float(o["alan"]) for o in odalar if o.get("tip") in DOLASIM_TIPLER)
    ortak = sum(float(o["alan"]) for o in odalar if o.get("tip") == "ortak")
    brut = float(brut_alan) if brut_alan else None

    ozet = {
        "oda_toplami_m2": round(toplam, 2),
        "net_kullanim_m2": round(net, 2),
        "dolasim_m2": round(dolasim, 2),
        "ortak_m2": round(ortak, 2),
        "brut_m2": brut,
        "net_brut_orani": round(net / brut, 3) if brut else None,
        "dolasim_orani": round(dolasim / toplam, 3) if toplam else None,
        "uyarilar": [],
    }
    if brut and toplam > brut:
        ozet["uyarilar"].append("Oda toplamı brüt alandan büyük: ölçüleri kontrol edin.")
    if brut and net / brut < 0.70:
        ozet["uyarilar"].append("Net/brüt oranı %70'in altında: verimlilik düşük olabilir.")
    if toplam and dolasim / toplam > 0.20:
        ozet["uyarilar"].append("Dolaşım payı %20'yi aşıyor: planlama gözden geçirilmeli.")
    return ozet


def tablo_satirlari(odalar: list[dict]) -> list[list]:
    toplam = sum(float(o["alan"]) for o in odalar) or 1
    return [[o["ad"], o.get("tip", "net"), float(o["alan"]),
             round(float(o["alan"]) / toplam * 100, 1)] for o in odalar]

