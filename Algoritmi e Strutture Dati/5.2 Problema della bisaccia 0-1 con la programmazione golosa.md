---
aliases: [PBPG, Problema bisaccia 0-1 programmazione golosa]
tags: [algoritmi-e-strutture-dati]
---
Dato un insieme di oggetti di dimensione e valore diverso, si desidera scegliere un sottoinsieme di oggetti da inserire in una bisaccia in modo tale da massimizzare il valore trasportato.

> **ALGORITMO EURISTICO:**
> ```cpp
> struct Toggetto { int costo, valore; }
>
> vector<bool> knapsack_01 (vector<Toggetto>& oggetto, int Cmax) {
> 	int n = oggetti.size();
> 	OrdinaPerValoreSpecificoDecrescente(oggetti); // costo O(n lg n)
>
> 	vector<bool> sol(n, false);
> 	int Cspeso = 0;
> 	for(int i = 0; i < n && Cspeso < Cmax; i++) {
> 		if(Cspeso + oggetti[i].costo <= Cmax) {
> 			sol[i] = true;
> 			Cspeso += oggetti[i].costo;;
> 		}
> 	}
>
> 	return sol;
> }
> ```

Ulteriore problema di questa tecnica è che 9 su 10 ci dà una risposta sbagliata ma, se ci accontentiamo di una approssimazione, allora è ottima. Un parametro qualitativo di algoritmi euristici è la cosiddetta **garanzia di errore** e cioè ci indica la tolleranza di errore dell'algoritmo (max *5% - 10%* ecc. di distacco dalla soluzione ottima.
