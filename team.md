# Vertikaalne Lõpmatus

                            ○
                            │
                            ∞
                            │
                            ○

                   VERTICAL · INFINITY



## Tiimi liikmed:
    Meeskond 8
- Andy Reiu (AR2147)
- Alex Ots (otsalex)


## Workflow:

### Üldine ###
- Esmaspäev ja teisipäev on aeg loengumaterjalidega tutvumiseks.
- Peale tööpäeva (~18:00) käiakse vähemalt korra Discordis.
- Kolmapäeval kell 17:00 toimub ühine koosolek Discordis.
- Jooksvalt võib plaan muutuda vastavalt vajadusele.

### Issue loomine ### 
- Kõik tööd on kirjeldatud GitHub Issues all.

### Feature branch’i loomine ###
- Iga Issue jaoks luuakse eraldi feature branch `dev` branch’ist. 
- Branch’i nimekuju võiks olla: feature/VL-<ISSUE_NR>-<PEREKONNANIMI>

### Arendus ja commit’id ###
- Töö tehakse ainult feature branch’is.
- Iga ülesanne sisaldab mitut commit’i.
- Commit message’id kirjeldavad selgelt tehtud muudatusi.
  Commit message: VL-<ISSUE_NR>-<PEREKONNANIMI> <type>: <lühike kirjeldus>
  (feat, fix, refactor, test etc)

### Push ja Pull Request ###
- Feature branch pushitakse GitHubi.
- Avatakse Pull Request `dev` branch’i suunas.
- Pull Request’i kirjeldus sisaldab vastava Issue sulgemise käsku: Closes #<ISSUE_NR>

### Code review ###
- Iga Pull Request vaadatakse üle vähemalt ühe tiimiliikme poolt.
- Review käigus jäetakse kommentaare ja vajadusel küsitakse muudatusi.
- Autor vastab kommentaaridele ja teeb vajadusel täiendavaid commit’e.

### Merge ja lõpetamine ###
- Pull Request’id merge’itakse GitHubi kaudu (mitte otse `dev` branch’i).
- Pärast merge’i suletakse seotud Issue automaatselt.
- Merge’i teeb Pull Request’i autor.