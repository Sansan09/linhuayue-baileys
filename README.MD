# <div align='center'>Wileys</div>

## ✨ Fitur Utama

- 🚀 **Modern & Cepat** - Dibangun dengan TypeScript dan teknologi terbaru
- 🔧 **Perbaikan @lid & @jid** - Mengatasi masalah @lid ke @pn di grup WhatsApp
- 📱 **Dukungan Multi-Device** - Mendukung koneksi multi-device WhatsApp
- 🔐 **Enkripsi End-to-End** - Komunikasi terenkripsi penuh
- 📨 **Semua Jenis Pesan** - Dukung teks, media, polling, dll.

## ⚠️ Peringatan

Proyek ini tidak berafiliasi, terkait, diotorisasi, didukung, atau terhubung secara resmi dengan WhatsApp atau anak perusahaannya. Situs resmi WhatsApp ada di whatsapp.com.

Pengelola Wileys tidak mendukung penggunaan aplikasi ini untuk melanggar Ketentuan Layanan WhatsApp. Kami menekankan tanggung jawab pribadi pengguna untuk menggunakan secara adil dan bertanggung jawab.

Gunakan dengan bijak. Hindari spam. Jangan gunakan otomatisasi berlebihan.


#### Pesan Teks

```javascript
await sock.sendMessage(jid, { text: 'halo dunia' })
```

#### Pesan Kutipan (bekerja dengan semua tipe)

```javascript
await sock.sendMessage(jid, { text: 'halo dunia' }, { quoted: message })
```

#### Sebut Pengguna (bekerja dengan sebagian besar tipe)

`@number` untuk sebut di teks, opsional.

```javascript
await sock.sendMessage(
    jid,
    {
        text: '@12345678901',
        mentions: ['12345678901@s.whatsapp.net']
    }
)
```

#### Teruskan Pesan

Butuh objek pesan, ambil dari store atau gunakan objek pesan.

```javascript
const msg = getMessageFromStore() // implementasikan sendiri
await sock.sendMessage(jid, { forward: msg })
```

#### Pesan Lokasi

```javascript
await sock.sendMessage(
    jid, 
    {
        location: {
            degreesLatitude: 24.121231,
            degreesLongitude: 55.1121221
        }
    }
)
```

#### Pesan Kontak

```javascript
const vcard = 'BEGIN:VCARD\n' 
            + 'VERSION:3.0\n' 
            + 'FN:Jeff Singh\n' 
            + 'ORG:Ashoka Uni;\n' 
            + 'TEL;type=CELL;type=VOICE;waid=911234567890:+91 12345 67890\n' 
            + 'END:VCARD'

await sock.sendMessage(
    id,
    { 
        contacts: { 
            displayName: 'Jeff', 
            contacts: [{ vcard }] 
        }
    }
)
```

#### Pesan Reaksi

Butuh kunci pesan, ambil dari store atau gunakan objek kunci.

```javascript
await sock.sendMessage(
    jid,
    {
        react: {
            text: '💖', // kosongkan untuk hapus reaksi
            key: message.key
        }
    }
)
```

#### Pin Pesan

Butuh kunci pesan.

Waktu:

| Waktu | Detik      |
|-------|------------|
| 24j   | 86.400     |
| 7h    | 604.800    |
| 30h   | 2.592.000  |

```javascript
await sock.sendMessage(
    jid,
    {
        pin: {
            type: 1, // 0 untuk hapus
            time: 86400,
            key: message.key
        }
    }
)
```

#### Pesan Poll

```javascript
await sock.sendMessage(
    jid,
    {
        poll: {
            name: 'Poll Saya',
            values: ['Opsi 1', 'Opsi 2', ...],
            selectableCount: 1,
            toAnnouncementGroup: false // atau true
        }
    }
)
```

### Kirim dengan Pratinjau Tautan

1. Secara default, WA tidak punya generasi tautan dari web.
2. Wileys punya fungsi untuk pratinjau tautan.
3. Tambahkan `link-preview-js` dengan `npm i link-preview-js`.
4. Kirim tautan:

```javascript
await sock.sendMessage(
    jid,
    {
        text: 'Halo, ini dikirim menggunakan https://npmjs.com/wileys'
    }
)
```


#### Pesan Gif

WA tidak dukung .gif, kirim sebagai .mp4 dengan flag `gifPlayback`.

```javascript
await sock.sendMessage(
    jid, 
    { 
        video: fs.readFileSync('Media/ma_gif.mp4'),
        caption: 'halo dunia',
        gifPlayback: true
    }
)
```

#### Pesan Video

```javascript
await sock.sendMessage(
    id, 
    { 
        video: {
            url: './Media/ma_gif.mp4'
        },
        caption: 'halo dunia',
        ptv: false // true untuk video note
    }
)
```

#### Pesan Audio

Konversi dengan ffmpeg: `codec: libopus`, `ac: 1`, `avoid_negative_ts`, `make_zero`.

Contoh: `ffmpeg -i input.mp4 -avoid_negative_ts make_zero -ac 1 output.ogg`

```javascript
await sock.sendMessage(
    jid, 
    {
        audio: {
            url: './Media/audio.mp3'
        },
        mimetype: 'audio/mp4'
    }
)
```

#### Pesan Gambar

```javascript
await sock.sendMessage(
    id, 
    { 
        image: {
            url: './Media/ma_img.png'
        },
        caption: 'halo dunia'
    }
)
```

#### Pesan View Once

Tambahkan `viewOnce: true` untuk semua pesan di atas.

```javascript
await sock.sendMessage(
    id, 
    { 
        image: {
            url: './Media/ma_img.png'
        },
        viewOnce: true, // bekerja dengan video, audio juga
        caption: 'halo dunia'
    }
)
```

## ✏️ Ubah Pesan

### Hapus Pesan (untuk semua)

```javascript
const msg = await sock.sendMessage(jid, { text: 'halo dunia' })
await sock.sendMessage(jid, { delete: msg.key })
```


### Edit Pesan

Gunakan konten editable.

```javascript
await sock.sendMessage(jid, {
      text: 'teks terupdate di sini',
      edit: response.key,
    });
```

### Unggah Ulang Pesan Media ke WhatsApp

WA hapus media lama dari server. Unggah ulang dengan:

```javascript
await sock.updateMediaMessage(msg)
```

## 📞 Tolak Panggilan

Ambil `callId` dan `callFrom` dari event `call`.

```javascript
await sock.rejectCall(callId, callFrom)
```

## 💬 Status Kirim di Obrolan

### Baca Pesan

Kumpulan kunci pesan harus ditandai dibaca secara eksplisit.

```javascript
const key: WAMessageKey
await sock.readMessages([key]) // bisa multiple kunci
```

ID pesan adalah pengenal unik. Akses dengan `message.key.id`.

### Update Kehadiran

`presence` bisa: available, composing, recording, paused, unavailable.

Berlaku 10 detik. Beri tahu jid apakah Anda online, offline, mengetik, dll.

```javascript
await sock.sendPresenceUpdate('available', jid) 
```

> [!NOTE] Jika klien desktop aktif, WA tidak kirim notifikasi push. Untuk terima notifikasi, setel offline dengan `sock.sendPresenceUpdate('unavailable')`.

## 📁 Ubah Obrolan

WA gunakan komunikasi terenkripsi untuk update obrolan/aplikasi.

> [!IMPORTANT] Jika salah update, WA bisa logout dari semua perangkat.

### Arsipkan Obrolan

```javascript
const lastMsgInChat = await getLastMessageInChat(jid) // implementasikan sendiri
await sock.chatModify({ archive: true, lastMessages: [lastMsgInChat] }, jid)
```

### Bisukan/Buka Bisukan Obrolan

Waktu dukung:

| Waktu | Milidetik   |
|-------|-------------|
| Hapus | null        |
| 8j    | 86.400.000  |
| 7h    | 604.800.000 |

```javascript
// Bisukan 8 jam
await sock.chatModify({ mute: 8 * 60 * 60 * 1000 }, jid)
// Buka bisukan
await sock.chatModify({ mute: null }, jid)
```

### Tandai Obrolan Dibaca/Tidak Dibaca

```javascript
const lastMsgInChat = await getLastMessageInChat(jid)
// tandai tidak dibaca
await sock.chatModify({ markRead: false, lastMessages: [lastMsgInChat] }, jid)
```

### Hapus Pesan untuk Saya

```javascript
await sock.chatModify(
    {
        clear: {
            messages: [
                {
                    id: 'ATWYHDNNWU81732J',
                    fromMe: true, 
                    timestamp: '1654823909'
                }
            ]
        }
    }, 
    jid
)
```

### Hapus Obrolan

```javascript
const lastMsgInChat = await getLastMessageInChat(jid)
await sock.chatModify({
        delete: true,
        lastMessages: [
            {
                key: lastMsgInChat.key,
                messageTimestamp: lastMsgInChat.messageTimestamp
            }
        ]
    },
    jid
)
```

### Pin/Hapus Pin Obrolan

```javascript
await sock.chatModify({
        pin: true // atau false untuk hapus pin
    },
    jid
)
```

### Bintangi/Hapus Bintang Pesan

```javascript
await sock.chatModify({
        star: {
            messages: [
                {
                    id: 'messageID',
                    fromMe: true // atau false
                }
            ],
            star: true // true: Bintangi; false: Hapus Bintang
        }
    },
    jid
)
```

### Pesan Menghilang

Ephemeral:

| Waktu | Detik       |
|-------|-------------|
| Hapus | 0           |
| 24j   | 86.400      |
| 7h    | 604.800     |
| 90h   | 7.776.000   |

Gunakan detik, default 7 hari.

```javascript
// Nyalakan pesan menghilang
await sock.sendMessage(
    jid, 
    { disappearingMessagesInChat: WA_DEFAULT_EPHEMERAL }
)

// Kirim sebagai pesan menghilang
await sock.sendMessage(jid, { text: 'halo' }, { ephemeralExpiration: WA_DEFAULT_EPHEMERAL })

// Matikan
await sock.sendMessage(
    jid, 
    { disappearingMessagesInChat: false }
)
```

## 👤 Query Pengguna

### Periksa ID Ada di WhatsApp

```javascript
const [result] = await sock.onWhatsApp(jid)
if (result.exists) console.log(`${jid} ada di WhatsApp, sebagai jid: ${result.jid}`)
```

### Query Riwayat Obrolan (grup juga)

Butuh pesan tertua di obrolan.

```javascript
const msg = await getOldestMessageInChat(jid) // implementasikan sendiri
await sock.fetchMessageHistory(
    50, // jumlah (max: 50 per query)
    msg.key,
    msg.messageTimestamp
)
```

Pesan diterima di event `messaging.history-set`.

### Ambil Status

```javascript
const status = await sock.fetchStatus(jid)
console.log('status: ' + status)
```

### Ambil Foto Profil (grup juga)

```javascript
// resolusi rendah
const ppUrl = await sock.profilePictureUrl(jid)
console.log(ppUrl)

// resolusi tinggi
const ppUrl = await sock.profilePictureUrl(jid, 'image')
```

### Ambil Profil Bisnis (deskripsi atau kategori)

```javascript
const profile = await sock.getBusinessProfile(jid)
console.log('deskripsi bisnis: ' + profile.description + ', kategori: ' + profile.category)
```

### Ambil Kehadiran Seseorang (mengetik atau online)

```javascript
sock.ev.on('presence.update', console.log)

await sock.presenceSubscribe(jid) 
```

## 🔄 Ubah Profil

### Ubah Status Profil

```javascript
await sock.updateProfileStatus('Halo Dunia!')
```

### Ubah Nama Profil

```javascript
await sock.updateProfileName('Nama Saya')
```

### Ubah Foto Tampilan (grup juga)

> [!NOTE] Seperti pesan media, gunakan `{ stream: Stream }` atau `{ url: Url }` atau Buffer, lihat [Pesan Media](#pesan-media).

```javascript
await sock.updateProfilePicture(jid, { url: './new-profile-picture.jpeg' })
```

### Hapus Foto Tampilan (grup juga)

```javascript
await sock.removeProfilePicture(jid)
```

## 👥 Grup

Untuk ubah properti grup, harus admin.

### Buat Grup

```javascript
const group = await sock.groupCreate('Grup Saya', ['1234@s.whatsapp.net', '4564@s.whatsapp.net'])
console.log('grup dibuat dengan id: ' + group.gid)
await sock.sendMessage(group.id, { text: 'halo semuanya' })
```

### Tambah/Hapus atau Turunkan/Naikkan

```javascript
await sock.groupParticipantsUpdate(
    jid, 
    ['abcd@s.whatsapp.net', 'efgh@s.whatsapp.net'],
    'add' // ganti dengan 'remove', 'demote', atau 'promote'
)
```

### Ubah Subjek (nama)

```javascript
await sock.groupUpdateSubject(jid, 'Subjek Baru!')
```

### Ubah Deskripsi

```javascript
await sock.groupUpdateDescription(jid, 'Deskripsi Baru!')
```

### Ubah Pengaturan

```javascript
// Hanya admin kirim pesan
await sock.groupSettingUpdate(jid, 'announcement')
// Semua bisa kirim
await sock.groupSettingUpdate(jid, 'not_announcement')
// Semua bisa ubah pengaturan grup
await sock.groupSettingUpdate(jid, 'unlocked')
// Hanya admin ubah pengaturan
await sock.groupSettingUpdate(jid, 'locked')
```

### Keluar Grup

```javascript
await sock.groupLeave(jid)
```

### Ambil Kode Undangan

Buat tautan: `'https://chat.whatsapp.com/' + code`.

```javascript
const code = await sock.groupInviteCode(jid)
console.log('kode grup: ' + code)
```

### Cabut Kode Undangan

```javascript
const code = await sock.groupRevokeInvite(jid)
console.log('Kode grup baru: ' + code)
```

### Gabung Menggunakan Kode Undangan

Kode tanpa `https://chat.whatsapp.com/`.

```javascript
const response = await sock.groupAcceptInvite(code)
console.log('bergabung ke: ' + response)
```

### Ambil Info Grup dari Kode Undangan

```javascript
const response = await sock.groupGetInviteInfo(code)
console.log('info grup: ' + response)
```

### Query Metadata (peserta, nama, deskripsi...)

```javascript
const metadata = await sock.groupMetadata(jid) 
console.log(metadata.id + ', judul: ' + metadata.subject + ', deskripsi: ' + metadata.desc)
```

### Gabung Menggunakan groupInviteMessage

```javascript
const response = await sock.groupAcceptInviteV4(jid, groupInviteMessage)
console.log('bergabung ke: ' + response)
```

### Ambil Daftar Permintaan Gabung

```javascript
const response = await sock.groupRequestParticipantsList(jid)
console.log(response)
```

### Setujui/Tolak Permintaan Gabung

```javascript
const response = await sock.groupRequestParticipantsUpdate(
    jid,
    ['abcd@s.whatsapp.net', 'efgh@s.whatsapp.net'],
    'approve' // atau 'reject'
)
console.log(response)
```

### Ambil Semua Metadata Grup yang Diikuti

```javascript
const response = await sock.groupFetchAllParticipating()
console.log(response)
```

### Toggle Ephemeral

Ephemeral:

| Waktu | Detik       |
|-------|-------------|
| Hapus | 0           |
| 24j   | 86.400      |
| 7h    | 604.800     |
| 90h   | 7.776.000   |

```javascript
await sock.groupToggleEphemeral(jid, 86400)
```

### Ubah Mode Tambah

```javascript
await sock.groupMemberAddMode(
    jid,
    'all_member_add' // atau 'admin_add'
)
```

## 🔒 Privasi

### Blokir/Buka Blokir Pengguna

```javascript
await sock.updateBlockStatus(jid, 'block') // Blokir
await sock.updateBlockStatus(jid, 'unblock') // Buka blokir
```

### Ambil Pengaturan Privasi

```javascript
const privacySettings = await sock.fetchPrivacySettings(true)
console.log('pengaturan privasi: ' + privacySettings)
```

### Ambil Daftar Blokir

```javascript
const response = await sock.fetchBlocklist()
console.log(response)
```

### Update Privasi Terakhir Dilihat

```javascript
const value = 'all' // 'contacts' | 'contact_blacklist' | 'none'
await sock.updateLastSeenPrivacy(value)
```

### Update Privasi Online

```javascript
const value = 'all' // 'match_last_seen'
await sock.updateOnlinePrivacy(value)
```

### Update Privasi Foto Profil

```javascript
const value = 'all' // 'contacts' | 'contact_blacklist' | 'none'
await sock.updateProfilePicturePrivacy(value)
```

### Update Privasi Status

```javascript
const value = 'all' // 'contacts' | 'contact_blacklist' | 'none'
await sock.updateStatusPrivacy(value)
```

### Update Privasi Tanda Terima Baca

```javascript
const value = 'all' // 'none'
await sock.updateReadReceiptsPrivacy(value)
```

### Update Privasi Tambah Grup

```javascript
const value = 'all' // 'contacts' | 'contact_blacklist'
await sock.updateGroupsAddPrivacy(value)
```

### Update Mode Menghilang Default

Ephemeral:

| Waktu | Detik       |
|-------|-------------|
| Hapus | 0           |
| 24j   | 86.400      |
| 7h    | 604.800     |
| 90h   | 7.776.000   |

```javascript
const ephemeral = 86400 
await sock.updateDefaultDisappearingMode(ephemeral)
```

## 📢 Daftar Siaran & Cerita

### Kirim Siaran & Cerita

Pesan bisa dikirim ke siaran & cerita. Tambahkan opsi pesan:

```javascript
await sock.sendMessage(
    jid,
    {
        image: {
            url: url
        },
        caption: caption
    },
    {
        backgroundColor: backgroundColor,
        font: font,
        statusJidList: statusJidList,
        broadcast: true
    }
)
```

Body pesan bisa `extendedTextMessage`, `imageMessage`, dll., lihat [Pesan Media](#pesan-media).  

`broadcast: true` aktifkan mode siaran.  

`statusJidList`: daftar penerima.  

ID siaran: `12345678@broadcast`.

### Query Penerima & Nama Daftar Siaran

```javascript
const bList = await sock.getBroadcastListInfo('1234@broadcast')
console.log(`nama daftar: ${bList.name}, penerima: ${bList.recipients}`)
```

## 🛠️ Fungsi Kustom

Wileys dirancang untuk fungsi kustom. Tambahkan ekstensi sendiri tanpa fork.

### Aktifkan Level Debug di Log Wileys

```javascript
const sock = makeWASocket({
    logger: P({ level: 'debug' }),
})
```

Ini tampilkan pesan WhatsApp di konsol.

### Cara WhatsApp Berkomunikasi

> [!TIP] Pelajari protokol WhatsApp dengan Libsignal Protocol dan Noise Protocol.

Contoh: Lacak persentase baterai ponsel. Aktifkan log, pesan baterai muncul:

```json
{
    "level": 10,
    "fromMe": false,
    "frame": {
        "tag": "ib",
        "attrs": {
            "from": "@s.whatsapp.net"
        },
        "content": [
            {
                "tag": "edge_routing",
                "attrs": {},
                "content": [
                    {
                        "tag": "routing_info",
                        "attrs": {},
                        "content": {
                            "type": "Buffer",
                            "data": [8,2,8,5]
                        }
                    }
                ]
            }
        ]
    },
    "msg":"communication"
}
```

`'frame'` punya: `tag` (jenis frame), `attrs` (metadata), `content` (data).  

### Daftarkan Callback untuk Event Websocket

> [!TIP] Lihat fungsi `onMessageReceived` di `socket.ts`.

```javascript
// Untuk tag 'edge_routing'
sock.ws.on('CB:edge_routing', (node: BinaryNode) => { })

// Dengan id 'abcd'
sock.ws.on('CB:edge_routing,id:abcd', (node: BinaryNode) => { })

// Dengan node routing_info
sock.ws.on('CB:edge_routing,id:abcd,routing_info', (node: BinaryNode) => { })
```

## 💡 Tips & Praktik Terbaik

- **Gunakan Caching**: Cache metadata grup untuk performa lebih baik.
- **Tangani Rekoneksi**: Implementasikan auto-reconnect untuk kestabilan.
- **Kelola Store**: Gunakan database (MongoDB, PostgreSQL) untuk produksi.
- **Penanganan Error**: Bungkus panggilan socket dengan try-catch.
- **Batas Rate**: Hormati batas WhatsApp untuk hindari ban—jangan spam.
- **Keamanan**: Jangan bagikan kredensial auth; gunakan variabel lingkungan.

---