# Zigbee Monitor per Home Assistant

Funziona con **ZHA** e/o **Zigbee2MQTT**, senza componenti HACS.

## File
| File | Dove va copiato |
|---|---|
| `custom_templates/zigbee_monitor.jinja` | `/config/custom_templates/zigbee_monitor.jinja` |
| `packages/zigbee_monitor.yaml` | `/config/packages/zigbee_monitor.yaml` |
| `dashboard/scheda_zigbee.yaml` | da incollare in una scheda "Manuale" della dashboard |

## Installazione
1. Se non usi già i *packages*, aggiungi a `configuration.yaml`:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
2. Copia i due file nelle cartelle indicate (crea le cartelle se mancano).
3. Nel package, nello script `zigbee_notifica`, sostituisci `notify.notify`
   con il tuo servizio (es. `notify.mobile_app_<nome_telefono>`).
4. Strumenti per sviluppatori → YAML → *Verifica configurazione*, poi **riavvia** HA.
5. Imposta la soglia batteria (consigliato 20%) dallo slider nella scheda.
6. Dashboard → Modifica → Aggiungi scheda → Manuale → incolla `scheda_zigbee.yaml`.

## Cosa fa
- `sensor.zigbee_dispositivi`: numero di dispositivi + attributo `elenco` con tutti i dettagli (ricalcolato ogni minuto).
- `sensor.zigbee_dispositivi_offline`, `sensor.zigbee_batterie_basse`, `sensor.zigbee_batteria_minima`.
- Notifica (push + campanella HA) quando un dispositivo diventa irraggiungibile e quando torna online.
- Notifica quando una batteria scende sotto soglia + promemoria ogni giorno alle 19:00 finché non viene sostituita.
- Nessun avviso nei primi 5 minuti dopo un riavvio; dopo 10 minuti arriva un riepilogo di chi è ancora offline.

## Note
- Un dispositivo è considerato offline quando **tutte** le sue entità sono `unavailable`.
  - **Zigbee2MQTT**: attiva *Settings → Availability* in Z2M, altrimenti i dispositivi non diventano mai "unavailable".
  - **ZHA**: i tempi sono in *Impostazioni → Dispositivi → ZHA → Configura* (di default 2 h per quelli a rete, 6 h per quelli a batteria).
- LQI: in Z2M il sensore `linkquality` è spesso disattivato, in ZHA il sensore `lqi` lo è di default: abilitali se vuoi vedere la colonna.
