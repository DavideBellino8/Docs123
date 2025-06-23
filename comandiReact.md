# Iron Temple – Setup & Development Guide

## React Setup

```bash
npm create vite@latest
```

---

## React Router Setup

```bash
npm i react-router
```

---

## Tailwind CSS Setup

```bash
npm install tailwindcss @tailwindcss/vite
```

**vite.config.ts:**

```ts
import { defineConfig } from "vite";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [tailwindcss()],
});
```

**index.css:**

```css
@import "tailwindcss";
```

---

## Recommended Extensions

- Prettier
- Material Icon Theme
- Tailwind Intellisense
- Tailwind Docs
- Thunder Client
- Path Intellisense
- Javascript Auto Backtick
- ES7+ React/Redux/React-Native Snippets

---

## Project Structure

Inside `src/`, add the following folders:

- `/components`
- `/hook`
- `/routes`
- `/service`

---

## React Router

**Routing structure to insert inside `App.tsx`:**

```tsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import ProtectedRoute from "./components/ProtectedRoute";
import MyBookings from "./routes/MyBookings";

<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/login" element={<Login />} />
    <Route path="/register" element={<Register />} />
    <Route path="/bookings/:id" element={<Bookings />} /> //rotta dinamica
    <Route element={<ProtectedRoute />}>
      {" "}
      //Rottaprotetta
      <Route path="/mybookings" element={<MyBookings />} />
    </Route>
  </Routes>
</BrowserRouter>;
```

**ProtectedRoute.tsx:**
//da mettere in una cartella al pari di /Home /bookings ecc

```tsx
import { Navigate, Outlet } from "react-router";

const ProtectedRoute = () => {
  const token = sessionStorage.getItem("token");
  return token ? <Outlet /> : <Navigate to="/login" replace />;
};

export default ProtectedRoute;
```

**Navigation between pages:**

```tsx
const navigate = useNavigate();
onClick={() => navigate("/login")}
```

**impostare una rotta dinamica**

```tsx
const navigate = useNavigate()
<div onClick={()=>navigate(`/show/${show.id}`)} className="max-w-max . . . .">
. . . .
</div>
```

**Recupero di un parametro di una rotta dinamica**

```tsx
const { id } = useParams(); //da usare in hook per maggiore sicurezza //nome del parametro deve coincidere con quello impostato della route in app.tsx route
```

---

## Example Custom Hooks

### GET Hook

```tsx
import { useEffect, useState } from "react";
import { trainingSessionApi } from "../service/trainingSessionGetApi";

const useGetTrainingSession = () => {
  const [trainingSession, setTrainingSession] = useState<TrainingSession[]>();

  useEffect(() => {
    const fetchTrainingSession = async () => {
      try {
        const equipmentData = await trainingSessionApi();
        setTrainingSession(equipmentData);
      } catch (err) {
        console.log(err);
      }
    };

    fetchTrainingSession();
  }, []);

  return trainingSession;
};

export default useGetTrainingSession;
```

### POST Hook

```tsx
import { useState } from "react";
import { trainingSessionPostApi } from "../service/trainingSessionPostApi";

const useBookingEquipment = (id: number) => {
  const [duration, setDuration] = useState(0);
  const [error, setError] = useState<string | undefined>("");

  const handleDuration = (time: number) => {
    setDuration(time);
  };

  const submitPrenotation = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    try {
      const res = await trainingSessionPostApi(duration, id);
      setError(res);
    } catch (err) {
      console.log(err);
    }
  };

  return { error, duration, handleDuration, submitPrenotation };
};

export default useBookingEquipment;
```

---

## API Calls

### GET with Token

```ts
let header = {
  "Content-Type": "application/json",
  Authorization: `Bearer ${token}`,
};

const res = await fetch(
  "https://d3660g9kardf5b.cloudfront.net/api/equipment-bookings",
  {
    method: "GET",
    headers: header,
  }
);
const data = await res.json();
return data;
```

### GET Equipment API

```ts
export const equipmentApi = async () => {
  const res = await fetch(
    "https://d3660g9kardf5b.cloudfront.net/api/equipment"
  );
  const data = await res.json();
  return data;
};
```

### POST Registration API

```ts
export const registrationApi = async (username: string, password: string) => {
  const res = await fetch(
    "https://d3660g9kardf5b.cloudfront.net/api/register",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ username, password }),
    }
  );

  return res;
};
```

---

## TypeScript Event Typings

- `onChange`: `React.ChangeEvent<HTMLInputElement>`
- `onSubmit`: `React.FormEvent<HTMLFormElement>`
- `onClick`: `React.MouseEvent<HTMLButtonElement>`

---

## `interfaces.d.ts`

- Use this file to define shared TypeScript interfaces

---

## Prebuilt UI Blocks

- **Signin / Registration**: [Tailwind Sign-in Forms](https://tailwindcss.com/plus/ui-blocks/application-ui/forms/sign-in-forms)
- **Navbar**: [Responsive Navbar Tutorial](https://medium.com/@ryaddev/build-responsive-navbar-with-tailwind-css-and-react-icons-3b13a272dec4)
- **Card**: [Tailwind Cards](https://v1.tailwindcss.com/components/cards)
- **Modal**: [Tailwind Modals](https://tailwindcss.com/plus/ui-blocks/application-ui/overlays/modal-dialogs)

## Preline

- **Preline**: [Preline](https://preline.co/examples.html)

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

- Frontend: React.js with TypeScript and Vite
- Styling: CSS3
- Authentication: JWT (JSON Web Token) for secure access to restricted areas

## Booking Management

- Users can book equipment in two ways:

  - Through an input field (up to 20 minutes)
  - Through quick-select buttons (5, 10, 15, 20 minutes)

- Bookings are visible:

  - In the public area if not logged in
  - In the private area if logged in

## Protection of "My Bookings" Area

- Access to the **My Bookings** page is protected by a `ProtectedRoute`
- It checks for a valid token in `sessionStorage`
- If no token is found, the user is redirected to the login page

## Registration System

- **On Submit**:

  - Prevents submission with empty fields
  - Rejects existing usernames

- **On Typing**:

  - Username must be ≥ 6 characters
  - Password must be ≥ 6 characters
  - Confirm password must match

## Login System

- Displays errors when:

  - Fields are empty
  - Credentials are invalid

- On successful login:

  - Receives and stores a token in `sessionStorage`
  - Redirects to the home page
  - Prevents access to login if already authenticated (redirects to logout)

## Logout System

- Clears the token from `sessionStorage` to log the user out

## Responsiveness

- Fully responsive design using Tailwind CSS

---

## UI Design

### Page Structure

- **Home Page**:  
  Hero message and equipment list
- **Bookings Page**:

  - Public Bookings: Viewable by everyone
  - My Bookings: Personal reservations (requires login)

- **Login/Registration Page**:  
  Authentication forms

### UI/UX Choices

- **Fixed Navbar**:  
  Quick navigation to main pages
- **Color Palette**:  
  Orange and gray – energy and strength
- **Fonts**:

  - Title (`h1`): `"Michroma", serif`
  - Body: `Arial, sans-serif`

- **Buttons**:  
  Large, high-contrast, with rounded corners
- **Messages**:  
  Clear error/success feedback for login, registration, and bookings
