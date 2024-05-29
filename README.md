SELECT ?museo ?nome ?luogo ?nomeLuogo ?coordinate ?anno ?visitatori #Uso SELECT e non SELECT DISTINCT perché altrimenti non avrei la possibilità di vedere come cambia il numero dei visitatori dello stesso museo nel corso degli anni.
WHERE {
?museo wdt:P31 wd:Q33506. #Istanza (wdt:P31) appartenente alla categoria dei musei (wd:Q33506)
?museo rdfs:label ?nome. #Recupero l'etichetta dell'elemento museo e la chiamo "nome".
  FILTER((LANG(?nome)) = "it") #Applico un filtro linguistico, voglio che tutti i nomi dei musei siano in lingua italiana. In questo modo evito doppioni nei risultati.
?museo wdt:P131 ?luogo; #wdt.P131 rappresenta l'unità amministrativa in cui è situato il singolo museo, do un nome a questa proprietà ("luogo"), lo inserisco nel SELECT per poi vederlo nei risultati.
(wdt:P131*) wd:Q1250. #Per identificare i musei situati nella regione Friuli Venezia Giulia (wd:Q1250), si utilizza un asterisco dopo wdt per garantire la ricorsività. Questo consente di risalire dall'unità amministrativa più piccola (comune o frazione) fino alla regione stessa. In questo modo, anche se la query restituisce inizialmente il nome di un comune o di una frazione, si procede a ritroso attraverso la provincia fino ad arrivare alla regione in cui il museo è ubicato, garantendo così l'accuratezza della ricerca.
?luogo rdfs:label ?nomeLuogo. #Recupero l'etichetta dell'elemento luogo e la chiamo nomeLuogo
FILTER((LANG(?nomeLuogo)) = "it") #Applico un filtro linguistico, voglio che tutti i nomi dei luoghi siano in lingua italiana.
OPTIONAL { ?museo wdt:P625 ?coordinate. } #Per ottenere anche le coordinate nei risultati.
OPTIONAL {
?museo p:P1174 ?nodointermedio. ?nodointermedio pq:P585 ?anno; #proprietà che denomino "anno". #"nodointermedio" poiché sono messi in relazione i visitatori annui con le date, ovvero l'"anno". 
ps:P1174 ?visitatori.} #Se disponibili voglio anche vedere i visitatori annui e l'anno di riferimento di ogni singolo museo. 
}
