# PROJECT SPECIFICATION
## Corpus-to-Co-Domain: Topological Stylometric Analysis & Paraphrase Preset Generator

### 1. Peran dan objektif

Bertindak sebagai gabungan **Computational Linguist, Stylometric Researcher, NLP Algorithm Designer, Applied Statistician, dan Front-End Engineer**.

Rancang dan implementasikan sebuah aplikasi berbasis HTML5, CSS, JavaScript, dan Bootstrap 5 untuk mengekstrak pola gaya penulisan dari sebuah target corpus (*domain*) serta mentransformasikannya menjadi konfigurasi instruksional parafrase (*co-domain*).

Tujuan aplikasi bukan sekadar menghitung kata, melainkan memodelkan hubungan antara distribusi leksikal, struktur sintaksis, dan organisasi diskursus untuk menghasilkan **writing-style fingerprint** yang dapat dioperasionalisasikan sebagai preset prompt.

Sistem harus dapat:

1. Menganalisis korpus berbahasa Indonesia, Inggris, maupun campuran.
2. Mengekstrak karakteristik kuantitatif dan kualitatif penulisan.
3. Mengidentifikasi distribusi, hubungan, dan perubahan pola dalam struktur teks.
4. Menyusun profil gaya yang didukung bukti korpus.
5. Mengubah profil tersebut menjadi beberapa preset prompt parafrase yang dapat disalin.
6. Menguji konsistensi algoritma dan menyatakan keterbatasan inferensinya.

**Prioritas:** validitas pengukuran dan inferensi harus mendahului kompleksitas visual aplikasi.

---

### 2. Definisi formal sistem

Definisikan:

- **D (Domain):** target corpus yang menjadi sumber pengamatan gaya penulisan.
- **θ (Parameters):** konfigurasi tokenisasi, filtering, ekstraksi fitur, pembobotan, dan klasifikasi.
- **A (Analysis Function):** fungsi pemetaan korpus ke representasi ciri penulisan.
- **S (Style Signature):** himpunan fitur terukur, pola relasional, dan interpretasi gaya yang dapat ditelusuri ke korpus.
- **K (Co-domain):** ruang kemungkinan preset prompt untuk merekonstruksi karakteristik gaya.
- **P (Generated Preset):** preset aktual yang dihasilkan dari S, dengan P sebagai anggota K.
- **T (Optional Input Text):** teks terpisah yang nantinya akan diparafrase menggunakan P.

Model operasional:

`D + θ → A(D, θ) → S → G(S, configuration) → P ∈ K`

Jika tersedia teks T dan sebuah language model eksternal, alur lanjutan adalah:

`P + T → Paraphrase`

Jangan mengasumsikan bahwa analisis n-gram saja dapat menghasilkan parafrase semantik berkualitas tinggi.

Pisahkan dengan jelas:
- Analisis korpus
- Inferensi gaya penulisan
- Konstruksi prompt
- Eksekusi parafrase

Aplikasi minimal wajib melaksanakan tiga tahap pertama. Eksekusi parafrase melalui model bahasa merupakan kemampuan opsional yang harus ditandai jika tidak diimplementasikan.

---

### 3. Corpus preprocessing pipeline

Bangun pipeline deterministik yang mencakup:

**3.1 Normalisasi**
- Unicode-aware text processing.
- Normalisasi whitespace.
- Pelestarian teks asli untuk pelacakan posisi dan kutipan.
- Case folding sebagai lapisan analisis terpisah.
- Penanganan tanda baca, singkatan, angka, kutipan, dan tanda hubung.
- Pemisahan paragraf dan kalimat.
- Language detection heuristik ID/EN, termasuk code-switching.

**3.2 Tokenization**
Setiap token harus memiliki metadata:

- Token asli
- Token ternormalisasi
- Indeks paragraf
- Indeks kalimat
- Posisi token dalam paragraf
- Posisi relatif dalam keseluruhan korpus
- Panjang token dalam karakter
- Kategori token jika dapat ditentukan

Pertahankan indeks asli agar setiap n-gram dapat dilacak kembali ke lokasi kemunculannya.

**3.3 Stopword dan stop-phrase**

Implementasikan daftar stopword/stop-phrase ID dan EN yang dapat dikonfigurasi.

Sediakan tiga mode:

1. Raw corpus — tanpa eliminasi.
2. Filtered corpus — eliminasi berdasarkan konfigurasi.
3. Comparative mode — membandingkan hasil raw dan filtered.

Jangan menerapkan eliminasi stopword secara destruktif pada seluruh analisis.

Konjungsi, negasi, modalitas, dan penanda transisi justru dapat menjadi indikator penting struktur retorika. Kata seperti "tidak", "bukan", "namun", "because", "although", dan "therefore" harus dapat dipertahankan secara selektif.

Sertakan dropdown konfigurasi dan custom stopword/stop-phrase input.

---

### 4. N-gram extraction engine

#### 4.1 Bigram

Ekstraksi bigram harus mendukung identifikasi kandidat fungsi:

- Connective atau transitional phrases.
- Declarative constructions.
- Associative constructions.
- Causal constructions.
- Contrastive constructions.
- Recurrent rhetorical expressions.
- Punctuation-aware bigrams.

Analisis punctuation dilakukan melalui token stream terpisah, termasuk pasangan kata–tanda baca, tanda baca–kata, serta pasangan tanda baca.

Jangan menyimpulkan fungsi linguistik hanya dari jumlah kemunculan bigram.

Setiap klasifikasi harus menggunakan konteks kemunculan dan diberi label sebagai *rule-based*, *context-supported*, atau *unverified*.

#### 4.2 Trigram

Ekstraksi trigram berfokus pada:

- Adverbial constructions.
- Adjectival constructions.
- Intensifiers dan qualifiers.
- Rhetorical transitions.
- Recurrent syntactic patterns.
- Frasa deskriptif dan evaluatif.

Gunakan POS tagging hanya apabila model atau resource yang diperlukan benar-benar tersedia.

Jika tidak, gunakan heuristik berbasis konteks dengan label keterbatasan. Jangan mengklaim memiliki dependency parser atau POS tagger jika implementasi hanya menggunakan pola string.

#### 4.3 Statistical features

Untuk setiap n-gram, hitung jika data mencukupi:

- Absolute frequency.
- Relative frequency per 100 tokens.
- Document frequency dengan paragraf sebagai unit dokumen.
- TF-IDF berbasis paragraf.
- Paragraph dispersion.
- Positional distribution.
- Burstiness.
- Co-occurrence strength.
- Contextual diversity.

Tampilkan rumus operasional atau definisi yang digunakan untuk metrik nonstandar.

Bedakan nilai frekuensi dari tingkat kepentingan semantik.

---

### 5. Frequency bands & semantic interpretation

Buat tiga kelompok distribusi n-gram.

**Lower Band**
- Default: kuantil 0–25%.
- Fokus: ekspresi jarang, variasi leksikal, dan kandidat pola idiosinkratik.
- Jangan langsung menginterpretasikan frekuensi rendah sebagai asumsi dasar penulis.

**Median Band**
- Default: kuantil 25–75%.
- Fokus: pola bahasa menengah, konsistensi konstruksi, dan distribusi penghubung gagasan.
- Analisis kemunculan lintas konteks untuk memeriksa kestabilan fungsi.

**Upper Band**
- Default: kuantil 75–100%.
- Fokus: konstruksi dominan, pengulangan retorika, dan pola kebahasaan berulang.
- Bedakan pola gaya dari pengulangan topik.

Ketentuan algoritmik:

1. Kuantil dihitung berdasarkan distribusi frekuensi tipe n-gram yang telah didefinisikan.
2. Threshold harus dapat dimodifikasi melalui dropdown atau input.
3. Tentukan aturan penanganan frekuensi yang sama pada batas kuantil agar kelompok tetap deterministik.
4. Tampilkan jumlah type, token occurrence, dan threshold aktual setiap band.
5. Sediakan tampilan raw frequency dan normalized frequency.
6. Jangan menyebut setiap band sebagai representasi semantik tanpa bukti kontekstual.

Untuk menginterpretasikan fungsi semantik dan pragmatik, tambahkan **KWIC (Key Word in Context)** dan contextual concordance, sehingga pengguna dapat memeriksa kalimat asal setiap pola.

Pisahkan:

- Observed feature
- Contextual evidence
- Inferred rhetorical function
- Confidence / evidence status

---

### 6. Paragraph distribution & positional topology

Modelkan korpus sebagai urutan paragraf:

`D = {p₁, p₂, ..., pₙ}`

Untuk setiap paragraf, simpan:

- Paragraph ID
- Word count
- Sentence count
- Mean sentence length
- Lexical diversity
- N-gram occurrences
- Normalized n-gram density
- Posisi relatif dalam korpus
- Distribusi punctuation
- Distribusi kategori linguistik

Lakukan dua analisis panjang secara terpisah.

**A. Paragraph-length ratio**
Kelompokkan paragraf berdasarkan jumlah token:

- Short
- Medium
- Long

Gunakan quantile-based bins sebagai default, dengan threshold manual.

**B. Word-length distribution**
Analisis panjang kata dalam karakter dan proporsinya pada setiap paragraf.

Jangan menyamakan kedua pengukuran ini.

Untuk setiap n-gram, tampilkan:

1. Jumlah total kemunculan.
2. Jumlah kemunculan di masing-masing paragraf.
3. Nomor paragraf dan posisi kemunculan.
4. Posisi relatif paragraf terhadap keseluruhan teks.
5. Kepadatan kemunculan per 100 token.
6. Histori distribusi sepanjang urutan teks.

Visualisasikan dalam:
- Frequency table.
- Paragraph-position heatmap.
- Positional distribution chart.
- N-gram concordance viewer.

Anggap urutan paragraf sebagai **textual sequence**, bukan otomatis sebagai waktu historis penulisan.

---

### 7. Topological modeling

Gunakan pendekatan **weighted multilayer text graph**.

Definisikan node sebagai salah satu dari:

- Lexical unit.
- N-gram.
- Sentence.
- Paragraph.
- Discourse unit, jika klasifikasi didukung bukti.

Definisikan relasi antarnode secara eksplisit:

- Sequential adjacency.
- Within-paragraph co-occurrence.
- Shared lexical construction.
- Weighted lexical association.
- Rhetorical relation, jika memiliki bukti analitis.

Sediakan beberapa lapisan graf:

**Layer 1 — Lexical Topology**
Hubungan antara token, n-gram, dan ko-kemunculan.

**Layer 2 — Syntactic Pattern Network**
Hubungan pola kalimat atau konstruksi gramatikal yang dapat diidentifikasi.

**Layer 3 — Discourse Distribution**
Hubungan dan transisi pola antarparagraf.

Gunakan metrik jaringan yang relevan dan feasible:

- Node degree.
- Weighted degree.
- Connected components.
- Centrality.
- Community structure, apabila ukuran jaringan mencukupi.
- Positional transitions.

Definisikan edge direction, bobot, normalisasi, dan filter noise sebelum menghitung metrik.

**Batasan metodologis:** ini merupakan topologi dalam pengertian relasional/graf. Jangan mengklaim kontinuitas, homeomorfisme, atau invarian topologis matematis tanpa mendefinisikan struktur topologi dan transformasinya.

Jangan menganggap konektivitas graf sebagai bukti langsung kesamaan makna.

Tujuan pemodelan adalah mengidentifikasi bagaimana pola bahasa terorganisasi dan berubah sepanjang korpus.

---

### 8. Multidimensional stylometric profiling

Bangun tiga dimensi utama.

#### A. Lexical Dimension

Kuantitatif:
- TTR.
- Moving-Average TTR (MATTR).
- Average word length.
- Vocabulary frequency distribution.
- N-gram TF-IDF.
- Lexical repetition.
- Burstiness.

TTR harus diberi peringatan tentang sensitivitas terhadap panjang korpus.

Kualitatif:
- Diction.
- Lexical register.
- Technical jargon.
- Emotional coloration.
- Evaluative vocabulary.
- Concreteness versus abstraction.

#### B. Syntactic Dimension

Kuantitatif:
- Average sentence length.
- Sentence-length variance.
- Punctuation distribution.
- Clause markers.
- Active/passive indicators.
- Dependency depth apabila parser tersedia.

Kualitatif:
- Sentence complexity.
- Rhythm.
- Periodic versus cumulative construction.
- Coordination versus subordination.
- Rhetorical arrangement.
- Argumentative versus descriptive patterns.

Jangan menyamakan indikator permukaan dengan hasil parsing sintaksis sesungguhnya.

#### C. Discourse Dimension

Kuantitatif:
- N-gram positional density.
- Entity repetition.
- Paragraph-length variation.
- Transition density.
- Recurrent rhetorical markers.

Kualitatif:
- Framing.
- Narrative voice.
- Cohesion.
- Argument progression.
- Common-placing.
- Evidence presentation.
- Rhetorical temperament.

Jika sebuah karakteristik tidak dapat diidentifikasi dengan cukup bukti, keluarkan status `undetermined`, bukan nilai hasil tebakan.

---

### 9. Writing-style classification

Bangun profil gaya berdasarkan enam kelompok parameter.

**9.1 Genre**
Dropdown:
- Popular
- Investigative
- Opinion / Editorial
- Academic
- Essayistic
- Journalistic
- Technical
- Hybrid
- Undetermined

Izinkan multilabel classification.

**9.2 Era**
Dropdown:
- Classical
- Early Modern
- Modern
- Contemporary
- Custom Period
- Unknown

Era merupakan metadata atau hipotesis berbasis bukti, bukan hasil pasti dari pilihan diksi.

**9.3 Audience**
Dropdown:
- General Public
- Students
- Academic Readers
- Professional Readers
- Specialist Readers
- Policy / Institutional Readers
- Mixed
- Unknown

**9.4 Framing Model**
Dropdown:
- Expository
- Problem–Solution
- Cause–Effect
- Comparative
- Dialectical
- Narrative
- Critical / Counterargument
- Interpretive
- Hybrid
- Undetermined

**9.5 Common-placing**
Operasionalisasikan sebagai cara penulis menggunakan bahan bersama atau topoi retoris untuk mengembangkan argumen.

Dropdown:
- Definition
- Analogy
- Historical Example
- Empirical Evidence
- Personal Anecdote
- Authority / Citation
- Common Knowledge
- Contrast
- Hypothetical Case
- Mixed
- Undetermined

**9.6 Temperament**
Dropdown:
- Analytical
- Assertive
- Skeptical
- Polemical
- Reflective
- Exploratory
- Detached
- Persuasive
- Ironic
- Hybrid
- Undetermined

Pisahkan *temperament of textual expression* dari kepribadian atau keyakinan penulis.

Setiap klasifikasi harus menyertakan:
- Label.
- Evidence excerpt.
- Supporting paragraph IDs.
- Heuristic justification.
- Evidence status.
- Manual override.

Parameter yang tidak tersedia atau null tidak boleh diisi dengan fakta fiktif. Gunakan default dropdown `Unknown / Undetermined / Manual Selection` sesuai konteks.

---

### 10. Heuristic inference engine

Susun heuristik dengan bentuk eksplisit:

`Feature → Contextual Pattern → Candidate Interpretation → Evidence → Limitation`

Contoh mekanisme:

- Pengulangan connective adversatif pada beberapa paragraf dapat menjadi indikator konstruksi kontras.
- Dominasi kalimat pendek belum cukup untuk menyimpulkan temperament assertive.
- Frekuensi modalitas tinggi dapat menunjukkan kecenderungan epistemik tertentu, tetapi harus dianalisis berdasarkan fungsi aktualnya.
- Kepadatan kutipan dapat menjadi indikator gaya evidensial, bukan bukti otomatis kualitas akademis.
- Repetisi entitas dapat berasal dari topik tulisan, bukan gaya individual.

Pisahkan:

1. Directly measured properties.
2. Rule-based hypotheses.
3. Context-supported interpretations.
4. Unsupported or unavailable conclusions.

Jangan membuat satu skor universal "style similarity" tanpa menjelaskan dimensi, bobot, dan validitas pembandingnya.

Sediakan pengaturan bobot fitur dan tunjukkan dampaknya terhadap profil gaya.

---

### 11. Co-domain preset generator

Hasil analisis harus dikonversi menjadi **copyable paraphrase prompt presets**.

Minimal sediakan:

**Preset A — High-Fidelity Stylistic Reconstruction**

Mengutamakan pola sintaksis, ritme, register, connective usage, dan organisasi diskursus yang didukung korpus.

**Preset B — Rhetorical Structure Transfer**

Mengutamakan framing, progression of argument, contrast, evidence placement, dan common-placing.

**Preset C — Lexical & Syntactic Adaptation**

Mengutamakan diksi, variasi kalimat, distribusi connective, kompleksitas sintaksis, dan pola transisi.

**Preset D — Balanced Semantic Paraphrase**

Menggabungkan karakteristik gaya yang dapat dipercaya sambil memprioritaskan kesetiaan makna.

Setiap preset harus memuat:

- Task definition.
- Target style signature.
- Measured characteristics.
- Supported rhetorical tendencies.
- Transformation instructions.
- Semantic invariants.
- Negative constraints.
- Validation checklist.
- Uncertain properties excluded from mandatory instructions.

**Semantic invariants:**

Parafrase harus mempertahankan proposisi inti, hubungan logis, negasi, entitas, atribusi, angka, tingkat kepastian, dan posisi argumentatif teks sumber.

Dilarang:
- Mengarang fakta atau referensi.
- Mengubah posisi argumen tanpa instruksi.
- Mengganti istilah teknis yang berakibat pada perubahan konsep.
- Menyalin ungkapan panjang dari korpus sebagai substitusi parafrase.
- Menggunakan gaya yang hanya diasumsikan tanpa bukti.

Preset harus cukup spesifik untuk merepresentasikan pola gaya, tetapi tidak berubah menjadi instruksi imitasi literal.

Sediakan:
- Preview lengkap.
- Editable prompt textarea.
- Copy to Clipboard.
- Reset Preset.
- Export Preset JSON jika memungkinkan.

Pisahkan input **Style Reference Corpus** dari **Text to Paraphrase**.

---

### 12. HTML Bootstrap application specification

Bangun aplikasi responsive berbasis Bootstrap 5, HTML5, CSS, dan vanilla JavaScript.

**Layout utama:**

**Panel 1 — Corpus Input**
- Corpus textarea.
- Upload `.txt` atau `.md`.
- Language selector.
- Word and paragraph counter.
- Analysis button.
- Clear / reset control.

**Panel 2 — Preprocessing Configuration**
- Tokenization settings.
- Stopword mode.
- Custom stop-phrase input.
- Punctuation handling.
- N-gram size.
- Quantile thresholds.
- Normalization settings.

**Panel 3 — Statistical Analysis**
- Lexical statistics.
- Bigram and trigram tables.
- Frequency bands.
- Contextual concordance.
- Distribution metrics.

**Panel 4 — Topological Explorer**
- Paragraph-position heatmap.
- Weighted graph visualization.
- Frequency evolution.
- Relation filtering.
- Node and edge inspection.

**Panel 5 — Writing Style Profile**
- Genre.
- Era.
- Audience.
- Framing.
- Common-placing.
- Temperament.
- Evidence and confidence inspection.
- Manual dropdown override.

**Panel 6 — Co-domain Generator**
- Preset selector.
- Optional text-to-paraphrase input.
- Prompt preview.
- Copyable text block.
- Export controls.

Gunakan visual hierarchy yang bersih, tipografi terbaca, collapsible advanced settings, dan desain desktop-first yang tetap usable pada mobile.

Seluruh kontrol harus memiliki fungsi. Jangan membuat dropdown, chart, atau tombol dekoratif yang tidak memengaruhi state aplikasi.

---

### 13. Runtime constraints

Implementasikan sebagai satu file `index.html` yang dapat dibuka langsung di browser.

Ketentuan:
- Vanilla JavaScript untuk analisis inti.
- Bootstrap 5 melalui CDN.
- Tidak membutuhkan build tool.
- Tidak membutuhkan backend.
- Tidak membutuhkan API key.
- Tidak menyimpan korpus pada server.
- Tidak melakukan network request untuk memproses teks.
- Gunakan escaping yang aman ketika merender teks pengguna.
- Berikan fallback untuk kegagalan akses Clipboard API.
- Analisis inti tetap berfungsi meskipun resource visual eksternal gagal dimuat.

Library tambahan hanya diperbolehkan apabila manfaatnya jelas, tersedia secara nyata, dan tidak menciptakan ketergantungan backend tersembunyi.

Fitur yang membutuhkan NLP model kompleks harus dipisahkan dari baseline implementation dan diberi label sesuai status implementasinya.

---

### 14. Algorithm testing & falsification

Sebelum menyatakan aplikasi selesai, uji algoritma dengan fixture yang representatif.

**Test A — Tokenization Integrity**
Input mengandung tanda baca berulang, kutipan, singkatan, Unicode, serta teks ID/EN campuran.

Verifikasi bahwa token dan posisi paragraf konsisten.

**Test B — N-gram Accounting**
Gunakan korpus kecil dengan hitungan bigram dan trigram yang dapat diperiksa manual.

Verifikasi absolute frequency, relative frequency, serta paragraph occurrence.

**Test C — Stopword Ablation**
Bandingkan raw dan filtered corpus.

Verifikasi bahwa hasil analisis connective dan negasi tidak hilang tanpa jejak.

**Test D — Frequency Band Boundaries**
Gunakan distribusi dengan nilai frekuensi yang sama.

Verifikasi bahwa aturan kuantil deterministik, tanpa duplikasi atau kehilangan tipe n-gram.

**Test E — Paragraph Perturbation**
Pindahkan urutan paragraf tanpa mengubah isi.

Frekuensi n-gram internal paragraf harus tetap sama, sementara distribusi posisi berubah sesuai relokasi.

**Test F — Corpus Repetition**
Duplikasikan korpus.

Periksa sifat metrik yang seharusnya tetap stabil dan identifikasi metrik yang sensitif terhadap ukuran.

**Test G — Insufficient Evidence**
Uji korpus sangat pendek, satu paragraf, atau kalimat tunggal.

Sistem harus mengembalikan `insufficient data` ketika metrik tidak layak dihitung.

**Test H — Semantic Counterexample**
Gunakan pasangan kalimat yang berbagi banyak token tetapi berbeda karena negasi atau relasi logis.

Pastikan sistem tidak mengklaim kemiripan semantik hanya berdasarkan lexical overlap.

**Test I — UI & Security**
Uji input kosong, input panjang, karakter HTML, reset state, perubahan dropdown, serta clipboard fallback.

Catat untuk setiap pengujian:
- Input.
- Expected output.
- Actual output.
- PASS / FAIL / NOT EXECUTED.
- Kegagalan yang terdeteksi.
- Perbaikan yang dilakukan.

Jangan menyatakan suatu pengujian PASS apabila belum benar-benar dijalankan.

---

### 15. Implementation sequence

Kerjakan secara berurutan:

**Phase 1 — Conceptual & Measurement Design**

Tentukan unit analisis, definisi fitur, rumus, asumsi, keterbatasan, dan relasi yang dimodelkan.

**Phase 2 — Algorithm & Heuristic Design**

Bangun preprocessing, n-gram engine, distribution model, inference rules, dan graph representation.

**Phase 3 — Implementation**

Implementasikan analisis yang benar-benar dapat berjalan dan terhubung dengan state HTML.

**Phase 4 — Validation**

Lakukan pengujian deterministik, boundary cases, counterexamples, dan pemeriksaan keterbatasan inferensi.

**Phase 5 — Prompt Generation**

Konversi fitur terverifikasi menjadi style signature dan co-domain prompt presets.

**Phase 6 — UI Integration & Final Verification**

Pastikan setiap parameter, hasil analisis, dan perubahan konfigurasi memengaruhi keluaran secara konsisten.

Jangan mengorbankan Phase 1–4 demi menghasilkan visualisasi lebih cepat.

---

### 16. Final deliverables

Hasil akhir wajib mencakup:

**A. Functional HTML Application**

Satu file `index.html` berisi implementasi Bootstrap UI, algoritma analisis, visualisasi yang memungkinkan, dan generator preset.

**B. Algorithm Documentation**

Penjelasan ringkas tentang:
- Definisi setiap metrik.
- Heuristik yang digunakan.
- Threshold dan parameter default.
- Asumsi.
- Keterbatasan.

**C. Validation Report**

Laporan pengujian beserta status aktualnya.

**D. Co-domain Output**

Preset prompt yang dapat diperiksa, diedit, dan disalin.

**E. Capability Disclosure**

Pisahkan dengan jelas:
- Fully implemented.
- Heuristically approximated.
- Requires external NLP model.
- Not implemented.

### 17. Acceptance criteria

Implementasi dianggap memenuhi spesifikasi hanya apabila:

1. Seluruh metrik yang ditampilkan memiliki definisi dan implementasi nyata.
2. N-gram dapat ditelusuri ke kemunculan dalam paragraf asal.
3. Pengubahan parameter menghasilkan perubahan analisis yang sesuai.
4. Lower, Median, dan Upper Band menggunakan aturan statistik yang terdokumentasi.
5. Inferensi retorika dibedakan dari pengukuran frekuensi.
6. Model graf memiliki definisi node, edge, dan pembobotan yang eksplisit.
7. Profil gaya dapat diubah melalui dropdown tanpa mengubah bukti korpus.
8. Co-domain presets benar-benar menggunakan hasil analisis, bukan deskripsi generik.
9. Copy-to-clipboard menghasilkan preset lengkap.
10. Pengujian dan keterbatasan dilaporkan tanpa mengarang keberhasilan.

**Prinsip akhir:** Aplikasi ini merupakan instrumen analisis dan rekonstruksi gaya berbasis bukti korpus, bukan mesin yang menganggap frekuensi kata sebagai penjelasan lengkap tentang makna, retorika, atau identitas penulis.
