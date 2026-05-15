import streamlit as st
import pandas as pd

# Konfiguracja strony
st.set_page_config(page_title="Symulator Ekstraklasy", layout="wide")
st.title("⚽ Symulator Ekstraklasy: Walka o Tytuł i Utrzymanie")

# Stan początkowy
dane_poczatkowe = {
    "Drużyna": [
        "Lech Poznań", "Jagiellonia Białystok", "Górnik Zabrze", "Raków Częstochowa",
        "GKS Katowice", "Zagłębie Lubin", "Wisła Płock", "Piast Gliwice",
        "Legia Warszawa", "Pogoń Szczecin", "Radomiak Radom", "Cracovia",
        "Śląsk Wrocław", "Puszcza Niepołomice", "Korona Kielce", "Lechia Gdańsk",
        "Arka Gdynia", "Bruk-Bet Termalica"
    ],
    "Pkt": [56, 52, 50, 49, 48, 48, 48, 45, 44, 43, 41, 40, 40, 39, 39, 38, 36, 28],
    "Bilans": [22, 18, 10, 15, 8, 5, 2, 0, 6, 4, -3, -5, -6, -8, -10, -12, -16, -30]
}

# Mecze do rozegrania
mecze_33 = [
    ("Wisła Płock", "Górnik Zabrze"), ("Radomiak Radom", "Lech Poznań"),
    ("Piast Gliwice", "Raków Częstochowa"), ("GKS Katowice", "Jagiellonia Białystok"),
    ("Lechia Gdańsk", "Legia Warszawa"), ("Arka Gdynia", "Bruk-Bet Termalica"),
    ("Pogoń Szczecin", "Cracovia"), ("Śląsk Wrocław", "Zagłębie Lubin"),
    ("Korona Kielce", "Puszcza Niepołomice")
]

mecze_34 = [
    ("Pogoń Szczecin", "GKS Katowice"), ("Cracovia", "Korona Kielce"),
    ("Raków Częstochowa", "Arka Gdynia"), ("Bruk-Bet Termalica", "Lechia Gdańsk"),
    ("Jagiellonia Białystok", "Zagłębie Lubin"), ("Lech Poznań", "Wisła Płock"),
    ("Górnik Zabrze", "Radomiak Radom"), ("Legia Warszawa", "Śląsk Wrocław"),
    ("Puszcza Niepołomice", "Piast Gliwice")
]

# Inicjalizacja wyników w sesji (aby pamiętało wybory)
if 'wyniki' not in st.session_state:
    st.session_state.wyniki = {}

# Funkcja do wyświetlania meczów
def wyswietl_kolejke(mecze, nazwa_kolejki):
    st.subheader(f"Kolejka {nazwa_kolejki}")
    for dom, wyjazd in mecze:
        klucz = f"{dom}-{wyjazd}"
        col1, col2 = st.columns([3, 2])
        with col1:
            st.write(f"**{dom}** vs **{wyjazd}**")
        with col2:
            wybor = st.radio(
                "Wynik", 
                options=["Brak", "1", "X", "2"], 
                horizontal=True, 
                key=klucz,
                label_visibility="collapsed"
            )
            st.session_state.wyniki[klucz] = {"dom": dom, "wyjazd": wyjazd, "typ": wybor}

# Główny układ strony
col_mecze, col_tabela = st.columns([1, 1.2])

with col_mecze:
    wyswietl_kolejke(mecze_33, "33")
    st.divider()
    wyswietl_kolejke(mecze_34, "34")

# Obliczanie nowej tabeli
df = pd.DataFrame(dane_poczatkowe)

for klucz, dane in st.session_state.wyniki.items():
    typ = dane["typ"]
    dom = dane["dom"]
    wyjazd = dane["wyjazd"]
    
    idx_dom = df.index[df['Drużyna'] == dom][0]
    idx_wyjazd = df.index[df['Drużyna'] == wyjazd][0]
    
    if typ == "1":
        df.at[idx_dom, 'Pkt'] += 3
        df.at[idx_dom, 'Bilans'] += 1
        df.at[idx_wyjazd, 'Bilans'] -= 1
    elif typ == "X":
        df.at[idx_dom, 'Pkt'] += 1
        df.at[idx_wyjazd, 'Pkt'] += 1
    elif typ == "2":
        df.at[idx_wyjazd, 'Pkt'] += 3
        df.at[idx_wyjazd, 'Bilans'] += 1
        df.at[idx_dom, 'Bilans'] -= 1

# Mock dla H2H (Zastąp to prawdziwą logiką meczów bezpośrednich w przyszłości)
# Obecnie używa długości nazwy drużyny jako sztucznego rozstrzygnięcia remisów przed bilansem
df['H2H_Mock'] = df['Drużyna'].apply(len) 

# Sortowanie: 1. Punkty, 2. Mecze bezpośrednie (Mock), 3. Bilans bramkowy
df = df.sort_values(by=['Pkt', 'H2H_Mock', 'Bilans'], ascending=[False, False, False]).reset_index(drop=True)
df.index += 1 # Pozycje od 1
df = df.drop(columns=['H2H_Mock'])

# Wyświetlanie tabeli
with col_tabela:
    st.subheader("Aktualna Tabela")
    # Stylowanie tabeli (wyróżnienie lidera i strefy spadkowej)
    def style_table(row):
        if row.name == 1:
            return ['background-color: #ffd70033'] * len(row) # Złoty dla 1 miejsca
        elif row.name >= 16:
            return ['background-color: #ff000033'] * len(row) # Czerwony dla spadku
        return [''] * len(row)
    
    st.dataframe(df.style.apply(style_table, axis=1), use_container_width=True, height=700)
