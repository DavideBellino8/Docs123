
```markdown
## INSTALLAZIONE BASE

```bash
npm install
npm -v
node -v
npm i -g @angular/cli
ng --version  # potrebbe dare problemi
ng new [nomeProgetto]
```

In caso di problemi:

Aprire PowerShell come amministratore e digitare:

```powershell
Set-ExecutionPolicy
```

Inserire:

```powershell
Unrestricted  RemoteSigned
```

Premere:

```powershell
T
```

Per avviare l'applicazione:

```bash
ng serve --open

```

---

## INSTALLAZIONE BOOTSTRAP

```bash
npm i bootstrap@latest
```

Nel file `angular.json` inserire:

```json
"styles": [
  "./node_modules/bootstrap/dist/css/bootstrap.css",
  "src/styles.css"
],
"scripts": [
  "./node_modules/bootstrap/dist/js/bootstrap.bundle.js"
]
```

---

## SVILUPPO

```html
<app-route></app-route>  <!-- contenitore principale -->
```

// Creare un nuovo componente:

```bash
ng g c nuovoComponent
```

// Creazione service:

```bash
ng g s [nome]
```

Nel service sotto il costruttore inserire:

```ts
private httpClient = inject(HttpClient)
```

Nel file `app.config.ts`:

```ts
provideHttpClient()
```

E poi importare.

Dichiarazione signal (simile a `useState` di React):

```ts
private corsi = signal<Equipment[]>([])
```

Creare una cartella `models` con un file `interface.d.ts` per tipizzare l’oggetto.

### Funzione per risolvere la chiamata API e salvare il dato nel signal:

```ts
public fetchEquipment() {
  return this.httpClient.get<Equipment[]>('https://d3660g9kardf5b.cloudfront.net/api/equipment');
}

loadEquipment() {
  this.fetchEquipment().subscribe({
    next: (data) => {
      this.corsi.set(data);
    },
  });
}
```

Solo lettura del dato:

```ts
fetchedEquipment = this.corsi.asReadonly();
```

Nel componente che usa i dati:

```ts
private corsiService = inject(CorsiService)

ngOnInit(): void {
  this.equipmentService.loadEquipment()
}

equipment = this.equipmentService.fetchedEquipment;
```

Nel file HTML:

```html
<div class="flex flex-wrap justify-center gap-8">
  @for (equipment of equipments(); track equipment) {
    <div class="max-w-sm rounded overflow-hidden shadow-lg">
      <img class="w-full" src={{equipment.image}} alt="Sunset in the mountains">
      <div class="px-6 py-4">
        <div class="font-bold text-xl mb-2">{{equipment.name}}</div>
        <p class="text-gray-700 text-base">
          {{equipment.claim}}
        </p>
      </div>
      <div class="flex justify-center">
        <button class="bg-green-500 mb-4 hover:bg-green-700 text-white font-bold py-2 px-4 rounded">
          Prenota corso
        </button>
      </div>
    </div>
  }
</div>
```

### Rendering condizionale (modale)

```html
<button (click)="onEquipmentBook()" class="bg-green-500 mb-4 hover:bg-green-700 text-white font-bold py-2 px-4 rounded">
  Prenota corso
</button>
```

Nel file TypeScript:

```ts
isBooking = false;

onEquipmentBook() {
  this.isBooking = !this.isBooking;
}
```

Nel file HTML:

```html
@if (isBooking) {
  <app-booking-modal></app-booking-modal>
}
```

Passaggio dell'oggetto al componente figlio:

```html
@if (isBooking) {
  <app-booking-modal [equipment]="equipment"></app-booking-modal>
}
```

Nel componente ricevente:

```ts
equipment = input.required<Equipment>();
```

Nel file HTML del figlio si può usare:

```ts
equipment().nomeProprietà
```

### Emissione evento dal figlio

Nel file HTML del figlio:

```html
<button (click)="onCloseModal()" type="button" class="mt-3 inline-flex w-full justify-center ...">Cancel</button>
```

Nel file TS:

```ts
close = output();

onCloseModal() {
  this.close.emit()
}
```

Nel file HTML del padre:

```html
<app-booking-modal (close)="onEquipmentBook()"></app-booking-modal>
```

---

## API IN POST con risposta testuale

```ts
public submitBooking(id: number, duration: number) {
  return this.httpClient
    .post<string>(
      `https://d3660g9kardf5b.cloudfront.net/api/equipment/${id}/book`,
      { duration: duration },
      { responseType: 'text' as 'json' }
    )
    .subscribe({
      next: (res) => {
        console.log(res);
        if (res === 'Equipment booked') {
          this.error = false;
        } else {
          this.error = true;
        }
      },
    });
}
```

Nel componente dove serve:

```ts
private equipmentService = inject(Equipments);

this.equipmentService.submitBooking(this.equipment().id, this.time());
```

---

## API CON TOKEN

```ts
public fetchEquipment() {
  const token = 'TUO_TOKEN'; // o da localStorage/sessionStorage

  return this.httpClient.get<Equipment[]>(
    'https://d3660g9kardf5b.cloudfront.net/api/equipment',
    {
      headers: {
        Authorization: `Bearer ${token}`
      }
    }
  );
}
```

---

## ROTTE DINAMICHE

Nel file `app.component.html`:

```html
<router-outlet />
```

Nel file `app.routes.ts`:

```ts
export const routes: Routes = [
  { path: '', component: Home },
  { path: 'dettaglio/:id', component: Dettaglio },
  { path: 'bookings', component: Bookings },
];
```

Nel file HTML per il link:

```html
<a href="" [routerLink]="[`/dettaglio/${equipment.id}`]">...</a>
```

Nel file TS del componente ricevente:

```ts
id = input.required<string>();
```

Nel file `app.config.ts`:

```ts
provideRouter(routes, withComponentInputBinding())
```

---

## COMPUTED

```ts
id = input.required<string>();
equipmentService = inject(Equipments);

ngOnInit() {
  this.equipmentService.loadEquipment();
}

equipments = this.equipmentService.fetchedEquipment;

equipment = computed(() =>
  this.equipmentService
    .fetchedEquipment()
    .find((eq) => eq.id.toString() === this.id())
);
```