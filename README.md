class MenuMakanan:
    # Class Attribute
    jumlah_menu = 0

    def __init__(self, kode, nama, harga):
        # Public Attributes
        self.kode = kode
        self.nama = nama
        # Private Attribute (Encapsulation)
        self.__harga = harga
        
        # Menambah jumlah menu setiap kali object dibuat
        MenuMakanan.jumlah_menu += 1

    # Property (Getter) untuk mengakses atribut private __harga
    @property
    def harga(self):
        return self.__harga

    # Setter untuk mengubah nilai __harga dengan validasi
    @harga.setter
    def harga(self, harga_baru):
        if harga_baru > 0:
            self.__harga = harga_baru
        else:
            print(f"[Error] Harga untuk {self.nama} harus lebih dari 0!")

    # Instance Method
    def info_menu(self):
        return f"[{self.kode}] {self.nama} - {Pesanan.format_rupiah(self.__harga)}"

    # Class Method
    @classmethod
    def get_total_menu(cls):
        return f"Total varian menu yang terdaftar: {cls.jumlah_menu} item"


class Pesanan:
    # Class Attribute
    pajak_ppn = 0.11  # PPN 11%

    def __init__(self, nama_pelanggan, nomor_meja):
        self.nama_pelanggan = nama_pelanggan
        self.nomor_meja = nomor_meja
        # Private Attribute (Encapsulation)
        self.__daftar_pesanan = []

    # Instance Method untuk menambah item ke pesanan
    def tambah_item(self, menu: MenuMakanan, jumlah: int):
        if jumlah > 0:
            self.__daftar_pesanan.append({"menu": menu, "jumlah": jumlah})
            print(f"Berhasil menambahkan {jumlah}x {menu.nama} ke pesanan {self.nama_pelanggan}.")
        else:
            print("Jumlah pesanan minimal 1.")

    # Instance Method untuk menghitung total dan mencetak struk
    def cetak_struk(self):
        print("\n" + "=" * 42)
        print(f"STRUK PEMESANAN - MEJA {self.nomor_meja}")
        print(f"Pelanggan : {self.nama_pelanggan}")
        print("-" * 42)
        
        subtotal = 0
        for item in self.__daftar_pesanan:
            menu = item["menu"]
            qty = item["jumlah"]
            total_item = menu.harga * qty
            subtotal += total_item
            print(f"{menu.nama} ({qty}x) \t: {self.format_rupiah(total_item)}")
            
        nominal_pajak = subtotal * Pesanan.pajak_ppn
        total_bayar = subtotal + nominal_pajak
        
        print("-" * 42)
        print(f"Subtotal \t\t: {self.format_rupiah(subtotal)}")
        print(f"PPN ({int(Pesanan.pajak_ppn * 100)}%) \t\t: {self.format_rupiah(nominal_pajak)}")
        print(f"TOTAL BAYAR \t\t: {self.format_rupiah(total_bayar)}")
        print("=" * 42 + "\n")

    # Class Method untuk mengubah persentase pajak secara global
    @classmethod
    def atur_pajak(cls, pajak_baru):
        if 0 <= pajak_baru <= 1:
            cls.pajak_ppn = pajak_baru
            print(f"[Info] Tarif PPN diperbarui menjadi {int(pajak_baru * 100)}%")
        else:
            print("[Error] Nilai pajak harus berada di antara 0.0 hingga 1.0")

    # Static Method sebagai fungsi bantuan formatting mata uang
    @staticmethod
    def format_rupiah(angka):
        return f"Rp {int(angka):,}".replace(",", ".")


# ==========================================
# EKSEKUSI PROGRAM (PEMBUATAN OBJECT)
# ==========================================
if __name__ == "__main__":
    print("--- DAFTAR MENU RESTORAN ---")
    # 1. Membuat Object dari class MenuMakanan
    menu1 = MenuMakanan("M01", "Nasi Goreng Spesial", 25000)
    menu2 = MenuMakanan("M02", "Ayam Bakar Madu", 30000)
    menu3 = MenuMakanan("M03", "Es Teh Manis", 5000)

    # Menampilkan info menu
    print(menu1.info_menu())
    print(menu2.info_menu())
    print(menu3.info_menu())
    
    # Memanggil Class Method dari MenuMakanan
    print(MenuMakanan.get_total_menu())
    print("-" * 30)

    # 2. Menguji Property & Setter (Encapsulation)
    print("\n--- UPDATE HARGA MENU ---")
    menu1.harga = -5000  # Akan ditolak oleh setter
    menu1.harga = 28000  # Berhasil diubah melalui setter
    print(f"Harga baru {menu1.nama}: {Pesanan.format_rupiah(menu1.harga)}\n")

    # 3. Membuat Object dari class Pesanan
    pesanan_andi = Pesanan("Andi", 5)
    pesanan_andi.tambah_item(menu1, 2)
    pesanan_andi.tambah_item(menu3, 2)

    # Mencetak struk pertama (PPN default 11%)
    pesanan_andi.cetak_struk()

    # 4. Menguji Class Method dari Pesanan (Mengubah PPN menjadi 10%)
    Pesanan.atur_pajak(0.10)
    
    pesanan_budi = Pesanan("Budi", 2)
    pesanan_budi.tambah_item(menu2, 1)
    pesanan_budi.tambah_item(menu3, 1)
    pesanan_budi.cetak_struk()
