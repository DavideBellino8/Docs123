
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

---

# Implementation of Choice

## Titolo
NOME progetto
Un'applicazione web per ..

## Descrizione
AngFitness è un'applicazione web sviluppata con Angular per fornire agli utenti la possibilità di gestire e prenotare i corsi disponibili all'interno della palestra. Nella Home , gli utenti trovano una breve introduzione alla palestra, che presenta i corsi principali offerti. Nella sezione Chi Siamo , è presente una spiegazione dettagliata della palestra, che spiega le funzionalità dei moderni macchinari presenti, ed espospone i servizi aggiuntivi. Gli utenti possono visualizzare i corsi disponibili e prenotarne uno o più di uno, a condizione che ci siano posti disponibili, semplicemente inserendo il proprio nome e cognome. Nella sezione Amministrazione , i gestori della palestra hanno la possibilità di aggiungere nuovi corsi fornendo tutte le informazioni necessarie, eliminare corsi esistenti tramite l'apposito bottone e visualizzare la lista completa degli utenti prenotati ai vari corsi.

## Versione tecnologie utilizzate
Angular CLI: 18.2.12
Typescript: 5.6.3 Node: 22.11.0
Package Manager: npm 10.9.0
Json-server: 1.0.0-beta.3

## Technologies Used

-   Frontend: React.js with TypeScript and Vite
    
-   Styling: CSS3
    
-   Authentication: JWT (JSON Web Token) for secure access to restricted areas
    

## Booking Management

-   Users can book equipment in two ways:
    
    -   Through an input field (up to 20 minutes)
        
    -   Through quick-select buttons (5, 10, 15, 20 minutes)
        
-   Bookings are visible:
    
    -   In the public area if not logged in
        
    -   In the private area if logged in
        

## Protection of "My Bookings" Area

-   Access to the **My Bookings** page is protected by a `ProtectedRoute`
    
-   It checks for a valid token in `sessionStorage`
    
-   If no token is found, the user is redirected to the login page
    

## Registration System

-   **On Submit**:
    
    -   Prevents submission with empty fields
        
    -   Rejects existing usernames
        
-   **On Typing**:
    
    -   Username must be ≥ 6 characters
        
    -   Password must be ≥ 6 characters
        
    -   Confirm password must match
        

## Login System

-   Displays errors when:
    
    -   Fields are empty
        
    -   Credentials are invalid
        
-   On successful login:
    
    -   Receives and stores a token in `sessionStorage`
        
    -   Redirects to the home page
        
    -   Prevents access to login if already authenticated (redirects to logout)
        

## Logout System

-   Clears the token from `sessionStorage` to log the user out
    

## Responsiveness

-   Fully responsive design using Tailwind CSS
    

---

## UI Design

### Page Structure

-   **Home Page**:  
    Hero message and equipment list
    
-   **Bookings Page**:
    
    -   Public Bookings: Viewable by everyone
        
    -   My Bookings: Personal reservations (requires login)
        
-   **Login/Registration Page**:  
    Authentication forms
    

### UI/UX Choices

-   **Fixed Navbar**:  
    Quick navigation to main pages
    
-   **Color Palette**:  
    Orange and gray – energy and strength
    
-   **Fonts**:
    
    -   Title (`h1`): `"Michroma", serif`
        
    -   Body: `Arial, sans-serif`
        
-   **Buttons**:  
    Large, high-contrast, with rounded corners
    
-   **Messages**:  
    Clear error/success feedback for login, registration, and bookings