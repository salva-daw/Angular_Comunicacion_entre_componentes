# Tema 5 - Comunicación entre Componentes (Angular)

## Índice

1. [Anidamiento de componentes](#1-anidamiento-de-componentes)  
2. [Comunicación padre-hijo](#2-comunicación-padre-hijo)  
3. [Comunicación hijo-padre](#3-comunicación-hijo-padre)  
4. [Comunicación bidireccional](#4-comunicación-bidireccional)  

---

## 1. Anidamiento de componentes

Podemos llamar a un componente dentro de otro mediante la etiqueta del selector definida en el decorador del componente.

### Ejemplo:

```bash
ng g c Components/header
```

**HTML (`header.component.html`):**

```html
<nav class="navbar navbar-light bg-light">
  <a class="navbar-brand" href="#">
    <h2 class="text-danger">DWEC (Tienda on line con Angular)</h2>
  </a>
  <img src="assets/cart.png" height="60px">
</nav>
```

**TypeScript (`header.component.ts`):**

```typescript
@Component({
  selector: 'app-header',
  standalone: true,
  imports: [],
  templateUrl: './header.component.html',
  styleUrl: './header.component.css'
})
export class HeaderComponent { }
```

**Incluir en `app.component.html`:**

```html
<app-header></app-header>
```

---

## 2. Comunicación padre-hijo

Un componente padre llama a un componente hijo y le pasa información usando _property binding_.

### Sintaxis:

```html
<etiqueta-hijo [dato]="valor"></etiqueta-hijo>
```

**En el hijo (`hijo.component.ts`):**

```ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-hijo',
  standalone: true,
  imports: [],
  templateUrl: './hijo.component.html',
  styleUrl: './hijo.component.css'
})
export class HijoComponent {
  @Input() dato!: number;
}
```

**HTML del hijo:**

```html
<h3>Componente Hijo</h3>
Dato desde el padre: {{dato}}
```

**En el padre (`padre.component.ts`):**

```ts
export class PadreComponent {
  variable: number = 0;

  incrementar() {
    this.variable++;
  }
}
```

**HTML del padre:**

```html
<h3>Componente Padre</h3>
<button class="btn btn-primary" (click)="incrementar()">Incrementar</button>
<app-hijo [dato]="variable"></app-hijo>
```

---

## 3. Comunicación hijo-padre

Se utiliza `@Output` y `EventEmitter` para emitir eventos personalizados del hijo al padre.

### Sintaxis:

```html
<etiqueta-hijo (eventoPersonalizado)="metodoPadre($event)"></etiqueta-hijo>
```

**En el hijo (`hijo.component.ts`):**
Modificado
```typescript
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'app-hijo',
  standalone: true,
  imports: [],
  templateUrl: './hijo.component.html',
  styleUrl: './hijo.component.css'
})
export class HijoComponent {
  @Input() dato!: number;
  @Output() mandaMensaje = new EventEmitter<string>();

  mensaje() {
    let mensaje = prompt("Dime mensaje") || "";
    this.mandaMensaje.emit(mensaje);
  }
}
```

**HTML del hijo:**

```html
<h3>Componente Hijo</h3>
dato desde el padre: {{dato}}  
<button class="btn btn-success" (click)="mensaje()">Mandar mensaje</button>
```

**En el padre (`padre.component.ts`):**

```ts
export class PadreComponent {
  variable: number = 0;
  mensaje: string = "";

  incrementar() {
    this.variable++;
  }

  muestraMensaje(dato: string) {
    this.mensaje = dato;
  }
}
```

**HTML del padre:**

```html
<h3>Componente Padre</h3>
Mensaje desde el hijo: {{mensaje}}  
<button class="btn btn-primary" (click)="incrementar()">Incrementar</button>

<app-hijo [dato]="variable" (mandaMensaje)="muestraMensaje($event)"></app-hijo>
```

---

## 4. Comunicación bidireccional

Se muestra una colección de datos en un componente hijo mediante `*ngFor`, y el hijo informa al padre de acciones a realizar.

**Componentes creados:**

```bash
ng g c /Components/cards
ng g c /Components/card
```

**HTML del padre (`cards.component.html`):**

```html
<h2>Comunicación Padre-Hijo con *ngFor Bidereccional</h2>
<div id="contenedor" class="row row-cols-1 row-cols-md-6 g-4">
  @for(articulo of articulos; track articulo.id){
    <app-card [articulo]="articulo" (borrarArticulo)="borrar($event)"></app-card>
  }@empty{
    <div class="alert alert-danger" role="alert">
        No hay articulos
    </div>
  }
</div>
```

**TypeScript del padre (`cards.component.ts`):**

```ts
import { ARTICULOS, Articulo } from '../../Modelos/articulo';

export class CardsComponent {
  articulos: Articulo[] = ARTICULOS;

  borrar(id: string) {
    const pos = this.articulos.findIndex(a => a.id == id);
    this.articulos.splice(pos, 1);
  }
}
```

**HTML del hijo (`card.component.html`):**

```html
<div class="card">
  <img src="assets/{{articulo.id}}.jpg">
  <div class="card-body">
    <h5 class="card-title">{{articulo.nombre}}</h5>
    <p class="card-text">{{articulo.descripcion}}</p>
    <b><p class="card-text text-center">{{articulo.precio}}</p></b>
    <div class="card-text text-center">
      <button (click)="borrar(articulo.id)" class="btn btn-danger">Borrar</button>
    </div>
  </div>
</div>
```

**TypeScript del hijo (`card.component.ts`):**

```ts
import { Articulo } from '../../Modelos/articulo';

export class CardComponent {
  @Input() articulo!: Articulo;
  @Output() borrarArticulo = new EventEmitter<string>();

  borrar(id: string) {
    this.borrarArticulo.emit(id);
  }
}
```

---

> Curso: Desarrollo Web en Entorno Cliente (DWC)  
> Tema 05: Comunicación entre componentes en Angular
