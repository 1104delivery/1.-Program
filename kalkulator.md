def startup(self):
    main_box = toga.Box(style=Pack(direction=COLUMN, padding=20, alignment="center"))

    judul = toga.Label("KALKULATOR BMI", style=Pack(font_size=24, font_weight='bold', padding=5, text_align="center", color="#333"))
    tombol_1 = toga.Button("Hitung BMI", on_press=self.buka_bmi, style=Pack(padding=15, background_color="#4CAF50", color="#fff", width=300, height=35))
    tombol_2 = toga.Button("Kalori Harian Ideal", on_press=self.buka_kalori, style=Pack(padding=5, background_color="#008CBA", color="#fff", width=300, height=35))

    main_box.add(judul)
    main_box.add(tombol_1)
    main_box.add(tombol_2)

    self.main_window = toga.MainWindow(title=self.formal_name)
    self.main_window.content = main_box
    self.main_window.show()

def buka_bmi(self, widget):
    box = toga.Box(style=Pack(direction=COLUMN, padding=10, alignment="center"))

    self.input_berat = toga.TextInput(placeholder="Berat (kg)", style=Pack(padding=10, width=300))
    self.input_tinggi = toga.TextInput(placeholder="Tinggi (cm)", style=Pack(padding=10, width=300))

    gender_box = toga.Box(style=Pack(direction=ROW, padding=5, alignment="center"))
    self.gender_pria = toga.Switch("Pria", style=Pack(padding_right=10))
    self.gender_wanita = toga.Switch("Wanita")
    gender_box.add(self.gender_pria)
    gender_box.add(self.gender_wanita)

    tombol_hitung = toga.Button("Hitung BMI", on_press=self.hitung_bmi, style=Pack(padding=10, background_color="#4CAF50", color="#fff", width=200, height=35))

    self.hasil_box = toga.Box(style=Pack(direction=COLUMN, padding=10, alignment="center", width=300))
    self.hasil_bmi = toga.Label("", style=Pack(padding=5, font_size=12, text_align="center"))
    self.hasil_box.add(self.hasil_bmi)

    box.add(toga.Label("Hitung BMI", style=Pack(font_size=24, font_weight='bold', padding_bottom=10)))
    box.add(self.input_berat)
    box.add(self.input_tinggi)
    box.add(gender_box)
    box.add(tombol_hitung)
    box.add(self.hasil_box)

    self.window_bmi = toga.MainWindow(title="BMI")
    self.window_bmi.content = box
    self.window_bmi.show()

def hitung_bmi(self, widget):
    try:
        if self.gender_pria.value and self.gender_wanita.value:
            self.hasil_bmi.text = "Pilih salah satu: Pria atau Wanita saja."
            return
        elif not self.gender_pria.value and not self.gender_wanita.value:
            self.hasil_bmi.text = "Silakan pilih jenis kelamin dulu."
            return
        
        berat = float(self.input_berat.value)
        tinggi_cm = float(self.input_tinggi.value)
        tinggi_m = tinggi_cm / 100
        bmi = berat / (tinggi_m ** 2)

        kategori = ""
        if bmi < 18.5:
            kategori = "Kurus"
        elif 18.5 <= bmi < 24.9:
            kategori = "Normal"
        elif 25 <= bmi < 29.9:
            kategori = "Gemuk"
        else:
            kategori = "Obesitas"

        # Rekomendasi Berat Ideal berdasarkan gender
        if self.gender_pria.value and not self.gender_wanita.value:
            bmi_ideal = 23
        elif self.gender_wanita.value and not self.gender_pria.value:
            bmi_ideal = 22
        else:
            bmi_ideal = 22.5  # default jika keduanya atau tidak dipilih

        berat_ideal = bmi_ideal * (tinggi_m ** 2)
        selisih = berat - berat_ideal

        rekomendasi = f"Berat ideal kamu = sekitar {berat_ideal:.1f} kg.\n"
        if selisih > 0:
            rekomendasi += f"Kamu perlu menurunkan sekitar {selisih:.1f} kg."
        elif selisih < 0:
            rekomendasi += f"Kamu perlu menaikkan sekitar {abs(selisih):.1f} kg."
        else:
            rekomendasi += "Berat kamu sudah ideal!"

        self.popup_custom("Hasil BMI", f"BMI kamu: {bmi:.2f} ({kategori})\n{rekomendasi}")
    except:
        self.hasil_bmi.text = "Input tidak valid"

def popup_custom(self, title, message):
    box = toga.Box(style=Pack(direction=COLUMN, padding=20, alignment="center"))

    label = toga.Label(message, style=Pack(font_size=14, padding_bottom=10, text_align="center", color="black"))
    button_tutup = toga.Button("Tutup", on_press=lambda w: self.popup_window.close(), style=Pack(padding_top=10, background_color="#e74c3c", color="white", width=100, height=30))

    box.add(label)
    box.add(button_tutup)

    self.popup_window = toga.Window(title=title)
    self.popup_window.content = box
    self.popup_window.size = (350, 200)
    self.popup_window.show()


def buka_kalori(self, widget):
    box = toga.Box(style=Pack(direction=COLUMN, padding=20, alignment="center"))

    self.input_berat_kalori = toga.TextInput(placeholder="Berat (kg)", style=Pack(padding=(5, 0), width=300))
    self.input_tinggi_kalori = toga.TextInput(placeholder="Tinggi (cm)", style=Pack(padding=(5, 0), width=300))
    self.input_usia_kalori = toga.TextInput(placeholder="Usia (tahun)", style=Pack(padding=(5, 0), width=300))

    self.gender_kalori = toga.Selection(
        items=["Pria", "Wanita"], 
        style=Pack(padding=(5, 0), width=300)
    )
    self.aktivitas_kalori = toga.Selection(
        items=[
            "Sedentari (Jarang olahraga)",
            "Ringan (1-3 hari/minggu)",
            "Sedang (3-5 hari/minggu)",
            "Berat (6-7 hari/minggu)",
            "Sangat berat (latihan harian)"
        ],
        style=Pack(padding=(5, 0), width=300)
    )
    self.tujuan_kalori = toga.Selection(
        items=[
            "Menurunkan berat badan",
            "Menjaga berat badan",
            "Menaikkan berat badan"
        ],
        style=Pack(padding=(5, 0), width=300)
    )

    tombol_hitung_kalori = toga.Button(
        "Hitung Kalori",
        on_press=self.hitung_kalori,
        style=Pack(padding=(10, 0), width=200, background_color="#008CBA", color="#fff", height=35)
    )

    box.add(toga.Label("Kalori Harian Ideal", style=Pack(font_size=20, font_weight="bold", padding=(10, 0))))
    box.add(self.input_berat_kalori)
    box.add(self.input_tinggi_kalori)
    box.add(self.input_usia_kalori)
    box.add(self.gender_kalori)
    box.add(self.aktivitas_kalori)
    box.add(self.tujuan_kalori)
    box.add(toga.Box(children=[tombol_hitung_kalori], style=Pack(alignment="center", padding=(10, 0))))

    self.window_kalori = toga.MainWindow(title="Kalori Harian Ideal")
    self.window_kalori.content = box
    self.window_kalori.show()

def hitung_kalori(self, widget):
    try:
        berat = float(self.input_berat_kalori.value)
        tinggi = float(self.input_tinggi_kalori.value)
        usia = int(self.input_usia_kalori.value)
        gender = self.gender_kalori.value
        aktivitas = self.aktivitas_kalori.value
        tujuan = self.tujuan_kalori.value

        if gender == "Pria":
            bmr = (10 * berat) + (6.25 * tinggi) - (5 * usia) + 5
        else:
            bmr = (10 * berat) + (6.25 * tinggi) - (5 * usia) - 161

        faktor = {
            "Sedentari (Jarang olahraga)": 1.2,
            "Ringan (1-3 hari/minggu)": 1.375,
            "Sedang (3-5 hari/minggu)": 1.55,
            "Berat (6-7 hari/minggu)": 1.725,
            "Sangat berat (latihan harian)": 1.9
        }

        kalori_total = bmr * faktor.get(aktivitas, 1.2)

        # Memanggil fungsi untuk mendapatkan saran berdasarkan tujuan
        saran = self.get_saran_kalori(kalori_total, tujuan)

        hasil_text = (
            f"Kalori dasar tubuhmu (BMR): {bmr:.0f} kkal\n"
            f"Kebutuhan kalori harian (termasuk aktivitas): {kalori_total:.0f} kkal\n"
            f"{saran}\n"
            "Ingat: Ini estimasi! Konsultasikan dengan ahli gizi untuk hasil terbaik."
        )

        # Menampilkan hasil di popup
        self.popup_custom("Hasil Kalori", hasil_text)

    except Exception as e:
        self.popup_custom("Error", "Input tidak valid. Mohon isi semua data dengan benar.")

def get_saran_kalori(self, kalori_total, tujuan):
    """
    Fungsi untuk memberikan saran kalori berdasarkan tujuan pengguna.
    """
    if tujuan == "Menurunkan berat badan":
        target_kalori = kalori_total - 500
        return f"Untuk menurunkan berat badan, konsumsi sekitar {target_kalori:.0f} kkal/hari."
    elif tujuan == "Menaikkan berat badan":
        target_kalori = kalori_total + 500
        return f"Untuk menaikkan berat badan, konsumsi sekitar {target_kalori:.0f} kkal/hari."
    else:
        return f"Untuk menjaga berat badan, konsumsi sekitar {kalori_total:.0f} kkal/hari."

def popup_custom(self, title, message):
    box = toga.Box(style=Pack(direction=COLUMN, padding=20, alignment="center"))

    label = toga.Label(message, style=Pack(font_size=14, padding_bottom=10, text_align="center", color="black"))
    button_tutup = toga.Button("Tutup", on_press=lambda w: self.popup_window.close(), style=Pack(padding_top=10, background_color="#e74c3c", color="white", width=100, height=30))

    box.add(label)
    box.add(button_tutup)

    self.popup_window = toga.Window(title=title)
    self.popup_window.content = box
    self.popup_window.size = (350, 200)
    self.popup_window.show()
