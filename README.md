# Analiza hantavirusa korišćenjem nukleotidnih k-mera


---

## O projektu

Hantavirusi su RNA virusi koji se prenose na ljude putem glodara. Ovaj rad ispituje da li je moguće automatski identifikovati vrstu hantavirusa isključivo na osnovu kompozicije nukleotidne sekvence, bez poravnanja (alignment-free pristup).

Analizirane su četiri vrste:

| Virus | Region | Bolest |
|-------|--------|--------|
| *Orthohantavirus hantanense* (Hantaan) | Azija | HFRS |
| *Orthohantavirus dobravaense* (Dobrava-Belgrade) | Balkan | HFRS |
| *Orthohantavirus puumalaense* (Puumala) | Severna Evropa | HFRS |
| *Orthohantavirus sinnombreense* (Sin Nombre) | Severna Amerika | HPS |

- **HFRS** (Hemorrhagic Fever with Renal Syndrome) - hemoragijska groznica sa bubrežnim sindromom
- **HPS** (Hantavirus Pulmonary Syndrome) - hantavirusni plućni sindrom

---

## Struktura projekta

```
hantavirus_kmer_analysis/
├── hantavirus_kmer_analysis.ipynb   # Glavni notebook
│
├── data/
│   ├── raw/
│   │   ├── all/                     # Sve sekvence (FASTA)
│   │   └── complete/                # Samo kompletni segmenti (FASTA)
│   └── processed/                   # K-mer matrice (CSV)
│
├── models/                          # Sačuvani modeli (PKL)
└── results/
    ├── figures/                     # Grafici (PNG)
    └── tables/                      # Tabele rezultata (CSV)
```

---

## Metodologija

1. **Učitavanje i filtriranje** - uklanjanje sekvenci sa ambiguous karakterima (N, R, Y...)
2. **Pregled i analiza podataka** - broj sekvenci po vrsti, dužine sekvenci, GC sadržaj
3. **K-mer frekvencije** - računanje normalizovanih vektora za k ∈ {3, 4, 5, 6}
4. **PCA** - redukcija dimenzionalnosti i vizualizacija separabilnosti klasa
5. **Klasifikacija** - 5 algoritama × 2 skupa × 4 vrednosti k = 40 modela
6. **Feature importance** - identifikacija najznačajnijih k-mera

### Klasifikatori

| Algoritam | Pristup |
|-----------|---------|
| Random Forest | Gradi veliki broj stabala odlučivanja na nasumičnim podskupovima podataka i kombinuje njihove predikcije kako bi doneo konačnu odluku |
| SVM (RBF kernel) | Pronalazi granicu koja maksimalno razdvaja klase, dok RBF kernel omogućava nelinearne granice u visokodimenzionalnom prostoru |
| K-Nearest Neighbors | Klasifikuje sekvencu prema tome kojoj vrsti pripada većina k njoj najbližih sekvenci iz trening skupa |
| Gradient Boosting | Sekvencijalno gradi niz slabih stabala, pri čemu svako naredno ispravlja greške prethodnog |
| Logistic Regression | Linearni model koji izračunava verovatnoću pripadnosti svakoj klasi na osnovu vrednosti feature-a |

### Evaluacija

- Stratifikovana 5-fold unakrsna validacija
- Train/test split 80/20 sa stratifikacijom
- Metrika: accuracy

---

## Rezultati

Na skupu kompletnih sekvenci (`complete`, 428 sekvenci):

| k | Najbolji model | Test Accuracy |
|---|---------------|---------------|
| 3 | Logistic Regression | 97.67% |
| 4 | KNN | **100%** |
| 5 | SVM / KNN | **100%** |
| 6 | Random Forest / LR | **100%** |

SVM (RBF) je najstabilniji algoritam kroz sve kombinacije skupova i vrednosti k.

---

## Pokretanje

### Zahtevi

```
python >= 3.8
biopython
numpy
pandas
scikit-learn
matplotlib
seaborn
joblib
jupyter
```

### Instalacija

```bash
pip install biopython numpy pandas scikit-learn matplotlib seaborn joblib jupyter
```

### Pokretanje notebooka

```bash
jupyter notebook hantavirus_kmer_analysis.ipynb
```

Ćelije se pokreću redom. Napomena: klasifikacija za k=6 na skupu `all` traje duže (~30 min).

### Podaci

Sekvence su preuzete sa [NCBI Virus](https://www.ncbi.nlm.nih.gov/labs/virus/) baze:

| Virus | TAXID |
|-------|-------|
| Hantaan virus | 3052480 |
| Dobrava-Belgrade virus | 3052477 |
| Puumala virus | 3052493 |
| Sin Nombre virus | 3052499 |

---

## Korišćene biblioteke

- [BioPython](https://biopython.org/) - parsiranje FASTA sekvenci
- [scikit-learn](https://scikit-learn.org/) - ML algoritmi, PCA, evaluacija
- [NumPy](https://numpy.org/) - k-mer računanje
- [pandas](https://pandas.pydata.org/) - tabelarni podaci
- [matplotlib](https://matplotlib.org/) / [seaborn](https://seaborn.pydata.org/) - vizualizacija
