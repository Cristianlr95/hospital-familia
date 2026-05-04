# Fase 5: Implementación - Frontend con Angular + Ionic

**Objetivo:** Implementar la capa de presentación con Angular 20 + Ionic 8 + Capacitor 8 para web y mobile.

**Alcance:**

- Estructura modular de Angular
- Autenticación y autorización en frontend
- Módulos: Auth, Patient, Event, Notification
- Componentes reutilizables
- Servicios HTTP con interceptores
- Guards de rutas
- Estados con servicios simples (o NgRx si es necesario)
- Formularios reactivos

**Duración estimada:** 4-5 sprints

---

## 1. Estructura del Proyecto

### 1.1 Directorios Base

```
hospital-familia-app/
├── src/
│   ├── app/
│   │   ├── core/
│   │   │   ├── guards/
│   │   │   │   ├── auth.guard.ts
│   │   │   │   └── role.guard.ts
│   │   │   ├── interceptors/
│   │   │   │   ├── auth.interceptor.ts
│   │   │   │   └── error.interceptor.ts
│   │   │   ├── models/
│   │   │   │   ├── auth.model.ts
│   │   │   │   ├── user.model.ts
│   │   │   │   └── api-response.model.ts
│   │   │   ├── services/
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── storage.service.ts
│   │   │   │   ├── notification.service.ts
│   │   │   │   └── http.service.ts
│   │   │   └── core.module.ts
│   │   ├── shared/
│   │   │   ├── components/
│   │   │   │   ├── header/
│   │   │   │   ├── footer/
│   │   │   │   ├── loading-spinner/
│   │   │   │   ├── error-message/
│   │   │   │   └── confirmation-dialog/
│   │   │   ├── pipes/
│   │   │   │   ├── safe.pipe.ts
│   │   │   │   └── date-format.pipe.ts
│   │   │   ├── directives/
│   │   │   │   ├── has-permission.directive.ts
│   │   │   │   └── highlight.directive.ts
│   │   │   └── shared.module.ts
│   │   ├── features/
│   │   │   ├── auth/
│   │   │   │   ├── pages/
│   │   │   │   │   ├── login/
│   │   │   │   │   ├── register/
│   │   │   │   │   ├── forgot-password/
│   │   │   │   │   └── reset-password/
│   │   │   │   ├── components/
│   │   │   │   │   └── login-form/
│   │   │   │   ├── services/
│   │   │   │   │   └── auth-state.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   ├── login-request.model.ts
│   │   │   │   │   └── register-request.model.ts
│   │   │   │   └── auth.module.ts
│   │   │   ├── patient/
│   │   │   │   ├── pages/
│   │   │   │   │   ├── patient-list/
│   │   │   │   │   ├── patient-detail/
│   │   │   │   │   ├── patient-form/
│   │   │   │   │   └── patient-events/
│   │   │   │   ├── services/
│   │   │   │   │   └── patient.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   └── patient.model.ts
│   │   │   │   └── patient.module.ts
│   │   │   ├── event/
│   │   │   │   ├── pages/
│   │   │   │   │   ├── event-list/
│   │   │   │   │   ├── event-detail/
│   │   │   │   │   └── event-form/
│   │   │   │   ├── services/
│   │   │   │   │   └── event.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   └── medical-event.model.ts
│   │   │   │   └── event.module.ts
│   │   │   ├── notification/
│   │   │   │   ├── components/
│   │   │   │   │   └── notification-center/
│   │   │   │   ├── services/
│   │   │   │   │   └── notification-hub.service.ts
│   │   │   │   ├── models/
│   │   │   │   │   └── notification.model.ts
│   │   │   │   └── notification.module.ts
│   │   │   └── dashboard/
│   │   │       ├── pages/
│   │   │       │   ├── dashboard-tutor/
│   │   │       │   ├── dashboard-paciente/
│   │   │       │   └── dashboard-doctor/
│   │   │       └── dashboard.module.ts
│   │   ├── app.component.ts
│   │   ├── app.component.html
│   │   ├── app.module.ts
│   │   ├── app.config.ts
│   │   ├── app-routing.module.ts
│   │   └── environment/
│   │       ├── environment.ts
│   │       └── environment.prod.ts
│   ├── assets/
│   ├── environments/
│   ├── styles/
│   │   ├── global.scss
│   │   ├── variables.scss
│   │   └── themes/
│   ├── index.html
│   └── main.ts
├── capacitor.config.ts
├── ionic.config.json
├── angular.json
├── package.json
└── tsconfig.json
```

### 1.2 package.json - Dependencias Clave

```json
{
  "dependencies": {
    "@angular/animations": "^20.0.0",
    "@angular/common": "^20.0.0",
    "@angular/compiler": "^20.0.0",
    "@angular/core": "^20.0.0",
    "@angular/forms": "^20.0.0",
    "@angular/platform-browser": "^20.0.0",
    "@angular/platform-browser-dynamic": "^20.0.0",
    "@angular/router": "^20.0.0",
    "@ionic/angular": "^8.0.0",
    "@ionic/core": "^8.0.0",
    "@capacitor/core": "^8.0.0",
    "@capacitor/app": "^8.0.0",
    "@capacitor/camera": "^8.0.0",
    "@capacitor/filesystem": "^8.0.0",
    "@capacitor/keyboard": "^8.0.0",
    "@capacitor/share": "^8.0.0",
    "@capacitor/splash-screen": "^8.0.0",
    "@capacitor/status-bar": "^8.0.0",
    "axios": "^1.6.0",
    "date-fns": "^3.0.0",
    "rxjs": "^7.8.0",
    "tslib": "^2.3.0",
    "zone.js": "^0.14.0"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "^20.0.0",
    "@angular/cli": "^20.0.0",
    "@angular/compiler-cli": "^20.0.0",
    "@ionic/angular-toolkit": "^9.0.0",
    "@types/jasmine": "^5.1.0",
    "@types/node": "^20.0.0",
    "jasmine-core": "^5.1.0",
    "karma": "^6.4.0",
    "karma-chrome-launcher": "^3.2.0",
    "karma-coverage": "^2.2.0",
    "karma-jasmine": "^5.1.0",
    "karma-jasmine-html-reporter": "^2.1.0",
    "typescript": "^5.9.0",
    "eslint": "^8.0.0",
    "@typescript-eslint/eslint-plugin": "^6.0.0",
    "@typescript-eslint/parser": "^6.0.0"
  }
}
```

---

## 2. Modelos y Interfaces

### 2.1 auth.model.ts

```typescript
export interface LoginRequest {
  email: string;
  password: string;
}

export interface RegisterRequest {
  email: string;
  username: string;
  password: string;
  confirmPassword: string;
  firstName: string;
  lastName: string;
  phoneNumber: string;
  roleName: string; // TUTOR, PACIENTE, DOCTOR
}

export interface LoginResponse {
  accessToken: string;
  refreshToken: string;
  tokenType: string;
  expiresIn: number;
  user: User;
}

export interface TokenRefreshRequest {
  refreshToken: string;
}

export interface AuthState {
  isAuthenticated: boolean;
  user: User | null;
  accessToken: string | null;
  refreshToken: string | null;
  loading: boolean;
  error: string | null;
}
```

### 2.2 user.model.ts

```typescript
export interface User {
  id: number;
  username: string;
  email: string;
  firstName: string;
  lastName: string;
  phoneNumber: string;
  profileImageUrl?: string;
  bio?: string;
  enabled: boolean;
  roles: string[];
  permissions: string[];
}

export interface UserProfile extends User {
  linkedPatients?: Patient[];
  specialization?: string; // Para doctores
  licenseNumber?: string; // Para doctores
}
```

### 2.3 api-response.model.ts

```typescript
export interface ApiResponse<T> {
  success: boolean;
  message: string;
  data: T | null;
  timestamp: string;
  path?: string;
  statusCode?: number;
}

export interface ApiError {
  statusCode: number;
  message: string;
  details?: string;
  timestamp: string;
}
```

### 2.4 patient.model.ts

```typescript
export interface Patient {
  id: number;
  firstName: string;
  lastName: string;
  dateOfBirth: string;
  gender: "M" | "F" | "O";
  medicalRecordNumber: string;
  bloodType?: string;
  allergies?: string[];
  chronicDiseases?: string[];
  medications?: string[];
  emergencyContact?: {
    name: string;
    phone: string;
    relationship: string;
  };
  tutor?: User;
  createdAt: string;
  updatedAt: string;
}
```

### 2.5 medical-event.model.ts

```typescript
export interface MedicalEvent {
  id: number;
  patientId: number;
  eventType:
    | "APPOINTMENT"
    | "CONSULTATION"
    | "TREATMENT"
    | "LAB_TEST"
    | "PRESCRIPTION";
  title: string;
  description: string;
  eventDate: string;
  duration?: number; // en minutos
  location?: string;
  doctor?: User;
  notes?: string;
  attachments?: Attachment[];
  status: "SCHEDULED" | "COMPLETED" | "CANCELLED";
  createdBy: User;
  createdAt: string;
  updatedAt: string;
}

export interface Attachment {
  id: number;
  fileName: string;
  fileUrl: string;
  fileType: string;
  uploadedAt: string;
}
```

### 2.6 notification.model.ts

```typescript
export interface Notification {
  id: number;
  userId: number;
  type: "INFO" | "WARNING" | "ERROR" | "SUCCESS" | "APPOINTMENT";
  title: string;
  message: string;
  read: boolean;
  actionUrl?: string;
  createdAt: string;
  expiresAt?: string;
}
```

---

## 3. Servicios Core

### 3.1 auth.service.ts

```typescript
import { Injectable } from "@angular/core";
import { HttpClient } from "@angular/common/http";
import { BehaviorSubject, Observable, throwError } from "rxjs";
import { tap, catchError, switchMap } from "rxjs/operators";
import {
  LoginRequest,
  LoginResponse,
  RegisterRequest,
  TokenRefreshRequest,
  AuthState,
  User,
} from "@core/models/index";
import { StorageService } from "./storage.service";
import { environment } from "@environments/environment";

@Injectable({
  providedIn: "root",
})
export class AuthService {
  private apiUrl = environment.apiUrl;
  private authStateSubject = new BehaviorSubject<AuthState>({
    isAuthenticated: false,
    user: null,
    accessToken: null,
    refreshToken: null,
    loading: false,
    error: null,
  });

  public authState$ = this.authStateSubject.asObservable();

  constructor(
    private http: HttpClient,
    private storage: StorageService,
  ) {
    this.loadStoredAuth();
  }

  private loadStoredAuth(): void {
    const token = this.storage.getAccessToken();
    const user = this.storage.getUser();

    if (token && user) {
      this.authStateSubject.next({
        isAuthenticated: true,
        user,
        accessToken: token,
        refreshToken: this.storage.getRefreshToken(),
        loading: false,
        error: null,
      });
    }
  }

  login(request: LoginRequest): Observable<LoginResponse> {
    return this.http
      .post<LoginResponse>(`${this.apiUrl}/auth/login`, request)
      .pipe(
        tap((response) => this.handleLoginSuccess(response)),
        catchError((error) => this.handleError(error)),
      );
  }

  register(request: RegisterRequest): Observable<User> {
    return this.http
      .post<User>(`${this.apiUrl}/auth/register`, request)
      .pipe(catchError((error) => this.handleError(error)));
  }

  refreshToken(): Observable<LoginResponse> {
    const refreshToken = this.storage.getRefreshToken();
    if (!refreshToken) {
      return throwError(() => new Error("No refresh token available"));
    }

    const request: TokenRefreshRequest = { refreshToken };
    return this.http
      .post<LoginResponse>(`${this.apiUrl}/auth/refresh`, request)
      .pipe(
        tap((response) => this.handleLoginSuccess(response)),
        catchError((error) => {
          this.logout();
          return this.handleError(error);
        }),
      );
  }

  logout(): void {
    this.storage.clearAuth();
    this.authStateSubject.next({
      isAuthenticated: false,
      user: null,
      accessToken: null,
      refreshToken: null,
      loading: false,
      error: null,
    });
  }

  isAuthenticated(): boolean {
    return this.authStateSubject.value.isAuthenticated;
  }

  getCurrentUser(): User | null {
    return this.authStateSubject.value.user;
  }

  hasRole(role: string): boolean {
    const user = this.getCurrentUser();
    return user ? user.roles.includes(role) : false;
  }

  hasPermission(permission: string): boolean {
    const user = this.getCurrentUser();
    return user ? user.permissions.includes(permission) : false;
  }

  private handleLoginSuccess(response: LoginResponse): void {
    this.storage.setTokens(response.accessToken, response.refreshToken);
    this.storage.setUser(response.user);

    this.authStateSubject.next({
      isAuthenticated: true,
      user: response.user,
      accessToken: response.accessToken,
      refreshToken: response.refreshToken,
      loading: false,
      error: null,
    });
  }

  private handleError(error: any): Observable<never> {
    const errorMessage = error.error?.message || "An error occurred";
    this.authStateSubject.next({
      ...this.authStateSubject.value,
      error: errorMessage,
      loading: false,
    });
    return throwError(() => error);
  }
}
```

### 3.2 storage.service.ts

```typescript
import { Injectable } from "@angular/core";
import { User } from "@core/models/index";

@Injectable({
  providedIn: "root",
})
export class StorageService {
  private readonly TOKEN_KEY = "hf_access_token";
  private readonly REFRESH_TOKEN_KEY = "hf_refresh_token";
  private readonly USER_KEY = "hf_user";

  constructor() {}

  setTokens(accessToken: string, refreshToken: string): void {
    this.setItem(this.TOKEN_KEY, accessToken);
    this.setItem(this.REFRESH_TOKEN_KEY, refreshToken);
  }

  getAccessToken(): string | null {
    return this.getItem(this.TOKEN_KEY);
  }

  getRefreshToken(): string | null {
    return this.getItem(this.REFRESH_TOKEN_KEY);
  }

  setUser(user: User): void {
    this.setItem(this.USER_KEY, JSON.stringify(user));
  }

  getUser(): User | null {
    const user = this.getItem(this.USER_KEY);
    return user ? JSON.parse(user) : null;
  }

  clearAuth(): void {
    this.removeItem(this.TOKEN_KEY);
    this.removeItem(this.REFRESH_TOKEN_KEY);
    this.removeItem(this.USER_KEY);
  }

  private setItem(key: string, value: string): void {
    localStorage.setItem(key, value);
  }

  private getItem(key: string): string | null {
    return localStorage.getItem(key);
  }

  private removeItem(key: string): void {
    localStorage.removeItem(key);
  }
}
```

### 3.3 http.service.ts

```typescript
import { Injectable } from "@angular/core";
import { HttpClient, HttpErrorResponse } from "@angular/common/http";
import { Observable, throwError } from "rxjs";
import { catchError } from "rxjs/operators";
import { ApiResponse } from "@core/models/index";

@Injectable({
  providedIn: "root",
})
export class HttpService {
  constructor(private http: HttpClient) {}

  get<T>(url: string): Observable<ApiResponse<T>> {
    return this.http
      .get<ApiResponse<T>>(url)
      .pipe(catchError(this.handleError));
  }

  post<T>(url: string, body: any): Observable<ApiResponse<T>> {
    return this.http
      .post<ApiResponse<T>>(url, body)
      .pipe(catchError(this.handleError));
  }

  put<T>(url: string, body: any): Observable<ApiResponse<T>> {
    return this.http
      .put<ApiResponse<T>>(url, body)
      .pipe(catchError(this.handleError));
  }

  delete<T>(url: string): Observable<ApiResponse<T>> {
    return this.http
      .delete<ApiResponse<T>>(url)
      .pipe(catchError(this.handleError));
  }

  patch<T>(url: string, body: any): Observable<ApiResponse<T>> {
    return this.http
      .patch<ApiResponse<T>>(url, body)
      .pipe(catchError(this.handleError));
  }

  private handleError(error: HttpErrorResponse) {
    let errorMessage = "An error occurred";

    if (error.error instanceof ErrorEvent) {
      errorMessage = `Error: ${error.error.message}`;
    } else {
      errorMessage = `Error Code: ${error.status}\nMessage: ${error.message}`;
    }

    console.error(errorMessage);
    return throwError(() => error);
  }
}
```

---

## 4. Interceptores

### 4.1 auth.interceptor.ts

```typescript
import { Injectable } from "@angular/core";
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor,
  HttpErrorResponse,
} from "@angular/common/http";
import { Observable, throwError, BehaviorSubject } from "rxjs";
import { catchError, filter, take, switchMap } from "rxjs/operators";
import { AuthService } from "@core/services/auth.service";

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  private isRefreshing = false;
  private refreshTokenSubject: BehaviorSubject<any> = new BehaviorSubject<any>(
    null,
  );

  constructor(private authService: AuthService) {}

  intercept(
    request: HttpRequest<unknown>,
    next: HttpHandler,
  ): Observable<HttpEvent<unknown>> {
    const token = localStorage.getItem("hf_access_token");

    if (token) {
      request = this.addToken(request, token);
    }

    return next.handle(request).pipe(
      catchError((error) => {
        if (error instanceof HttpErrorResponse && error.status === 401) {
          return this.handle401Error(request, next);
        } else {
          return throwError(() => error);
        }
      }),
    );
  }

  private addToken(request: HttpRequest<any>, token: string): HttpRequest<any> {
    return request.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`,
      },
    });
  }

  private handle401Error(
    request: HttpRequest<any>,
    next: HttpHandler,
  ): Observable<HttpEvent<any>> {
    if (!this.isRefreshing) {
      this.isRefreshing = true;
      this.refreshTokenSubject.next(null);

      return this.authService.refreshToken().pipe(
        switchMap((response: any) => {
          this.isRefreshing = false;
          this.refreshTokenSubject.next(response.accessToken);
          return next.handle(this.addToken(request, response.accessToken));
        }),
        catchError((err) => {
          this.isRefreshing = false;
          this.authService.logout();
          return throwError(() => err);
        }),
      );
    } else {
      return this.refreshTokenSubject.pipe(
        filter((token) => token != null),
        take(1),
        switchMap((token) => {
          return next.handle(this.addToken(request, token));
        }),
      );
    }
  }
}
```

### 4.2 error.interceptor.ts

```typescript
import { Injectable } from "@angular/core";
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor,
  HttpErrorResponse,
} from "@angular/common/http";
import { Observable, throwError } from "rxjs";
import { catchError } from "rxjs/operators";

@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  constructor() {}

  intercept(
    request: HttpRequest<unknown>,
    next: HttpHandler,
  ): Observable<HttpEvent<unknown>> {
    return next.handle(request).pipe(
      catchError((error: HttpErrorResponse) => {
        let errorMessage = "An error occurred";

        if (error.error instanceof ErrorEvent) {
          errorMessage = error.error.message;
        } else {
          errorMessage =
            error.error?.message ||
            `Error: ${error.status} ${error.statusText}`;
        }

        console.error(errorMessage);
        return throwError(() => new Error(errorMessage));
      }),
    );
  }
}
```

---

## 5. Guards

### 5.1 auth.guard.ts

```typescript
import { Injectable } from "@angular/core";
import { Router, CanActivateFn } from "@angular/router";
import { AuthService } from "@core/services/auth.service";

@Injectable({
  providedIn: "root",
})
export class authGuard implements CanActivateFn {
  constructor(
    private authService: AuthService,
    private router: Router,
  ) {}

  canActivate(): boolean {
    if (this.authService.isAuthenticated()) {
      return true;
    }

    this.router.navigate(["/auth/login"]);
    return false;
  }
}

export const authGuard: CanActivateFn = (route, state) => {
  const authService = new AuthService(null as any, null as any);

  if (authService.isAuthenticated()) {
    return true;
  }

  const router = inject(Router);
  router.navigate(["/auth/login"]);
  return false;
};
```

### 5.2 role.guard.ts

```typescript
import { Injectable, inject } from "@angular/core";
import { Router, CanActivateFn } from "@angular/router";
import { AuthService } from "@core/services/auth.service";

export const roleGuard = (requiredRole: string): CanActivateFn => {
  return (route, state) => {
    const authService = inject(AuthService);
    const router = inject(Router);

    if (authService.hasRole(requiredRole)) {
      return true;
    }

    router.navigate(["/unauthorized"]);
    return false;
  };
};
```

---

## 6. Módulos

### 6.1 core.module.ts

```typescript
import { NgModule } from "@angular/core";
import { CommonModule } from "@angular/common";
import { HttpClientModule, HTTP_INTERCEPTORS } from "@angular/common/http";

import { AuthService } from "./services/auth.service";
import { StorageService } from "./services/storage.service";
import { HttpService } from "./services/http.service";
import { AuthInterceptor } from "./interceptors/auth.interceptor";
import { ErrorInterceptor } from "./interceptors/error.interceptor";

@NgModule({
  declarations: [],
  imports: [CommonModule, HttpClientModule],
  providers: [
    AuthService,
    StorageService,
    HttpService,
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true,
    },
    {
      provide: HTTP_INTERCEPTORS,
      useClass: ErrorInterceptor,
      multi: true,
    },
  ],
})
export class CoreModule {}
```

### 6.2 shared.module.ts

```typescript
import { NgModule } from "@angular/core";
import { CommonModule } from "@angular/common";
import { IonicModule } from "@ionic/angular";
import { FormsModule, ReactiveFormsModule } from "@angular/forms";

// Components
import { HeaderComponent } from "./components/header/header.component";
import { LoadingSpinnerComponent } from "./components/loading-spinner/loading-spinner.component";
import { ErrorMessageComponent } from "./components/error-message/error-message.component";

// Pipes
import { SafePipe } from "./pipes/safe.pipe";
import { DateFormatPipe } from "./pipes/date-format.pipe";

// Directives
import { HasPermissionDirective } from "./directives/has-permission.directive";

@NgModule({
  declarations: [
    HeaderComponent,
    LoadingSpinnerComponent,
    ErrorMessageComponent,
    SafePipe,
    DateFormatPipe,
    HasPermissionDirective,
  ],
  imports: [CommonModule, IonicModule, FormsModule, ReactiveFormsModule],
  exports: [
    CommonModule,
    IonicModule,
    FormsModule,
    ReactiveFormsModule,
    HeaderComponent,
    LoadingSpinnerComponent,
    ErrorMessageComponent,
    SafePipe,
    DateFormatPipe,
    HasPermissionDirective,
  ],
})
export class SharedModule {}
```

### 6.3 auth.module.ts

```typescript
import { NgModule } from "@angular/core";
import { CommonModule } from "@angular/common";
import { ReactiveFormsModule } from "@angular/forms";
import { IonicModule } from "@ionic/angular";

import { AuthRoutingModule } from "./auth-routing.module";
import { LoginComponent } from "./pages/login/login.component";
import { RegisterComponent } from "./pages/register/register.component";
import { ForgotPasswordComponent } from "./pages/forgot-password/forgot-password.component";
import { ResetPasswordComponent } from "./pages/reset-password/reset-password.component";
import { SharedModule } from "@shared/shared.module";

@NgModule({
  declarations: [
    LoginComponent,
    RegisterComponent,
    ForgotPasswordComponent,
    ResetPasswordComponent,
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    IonicModule,
    AuthRoutingModule,
    SharedModule,
  ],
})
export class AuthModule {}
```

---

## 7. Enrutamiento

### 7.1 app-routing.module.ts

```typescript
import { NgModule } from "@angular/core";
import { PreloadAllModules, RouterModule, Routes } from "@angular/router";
import { authGuard, roleGuard } from "@core/guards/index";

const routes: Routes = [
  {
    path: "",
    redirectTo: "dashboard",
    pathMatch: "full",
  },
  {
    path: "auth",
    loadChildren: () =>
      import("./features/auth/auth.module").then((m) => m.AuthModule),
  },
  {
    path: "dashboard",
    loadChildren: () =>
      import("./features/dashboard/dashboard.module").then(
        (m) => m.DashboardModule,
      ),
    canActivate: [authGuard],
  },
  {
    path: "patient",
    loadChildren: () =>
      import("./features/patient/patient.module").then((m) => m.PatientModule),
    canActivate: [authGuard],
  },
  {
    path: "event",
    loadChildren: () =>
      import("./features/event/event.module").then((m) => m.EventModule),
    canActivate: [authGuard],
  },
  {
    path: "notification",
    loadChildren: () =>
      import("./features/notification/notification.module").then(
        (m) => m.NotificationModule,
      ),
    canActivate: [authGuard],
  },
  {
    path: "unauthorized",
    loadChildren: () =>
      import("./features/error/error.module").then((m) => m.ErrorModule),
  },
  {
    path: "**",
    redirectTo: "dashboard",
  },
];

@NgModule({
  imports: [
    RouterModule.forRoot(routes, {
      preloadingStrategy: PreloadAllModules,
    }),
  ],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

---

## 8. Componentes Base

### 8.1 login.component.ts

```typescript
import { Component, OnInit } from "@angular/core";
import { FormBuilder, FormGroup, Validators } from "@angular/forms";
import { Router } from "@angular/router";
import { AuthService } from "@core/services/auth.service";
import { AlertController, LoadingController } from "@ionic/angular";

@Component({
  selector: "app-login",
  templateUrl: "./login.component.html",
  styleUrls: ["./login.component.scss"],
})
export class LoginComponent implements OnInit {
  loginForm!: FormGroup;
  isLoading = false;
  isPasswordVisible = false;

  constructor(
    private formBuilder: FormBuilder,
    private authService: AuthService,
    private router: Router,
    private alertController: AlertController,
    private loadingController: LoadingController,
  ) {}

  ngOnInit(): void {
    this.initializeForm();
  }

  private initializeForm(): void {
    this.loginForm = this.formBuilder.group({
      email: ["", [Validators.required, Validators.email]],
      password: ["", [Validators.required, Validators.minLength(8)]],
    });
  }

  get email() {
    return this.loginForm.get("email");
  }

  get password() {
    return this.loginForm.get("password");
  }

  async onSubmit(): Promise<void> {
    if (this.loginForm.invalid) {
      await this.showAlert(
        "Error",
        "Por favor completa los campos correctamente",
      );
      return;
    }

    this.isLoading = true;
    const loading = await this.loadingController.create({
      message: "Iniciando sesión...",
    });
    await loading.present();

    this.authService.login(this.loginForm.value).subscribe(
      async (response) => {
        await loading.dismiss();
        this.isLoading = false;

        // Redireccionar según el rol del usuario
        const userRole = response.user.roles[0];
        this.router.navigate([`/dashboard/${userRole.toLowerCase()}`]);
      },
      async (error) => {
        await loading.dismiss();
        this.isLoading = false;
        await this.showAlert(
          "Error de autenticación",
          error.error?.message || "Credenciales inválidas",
        );
      },
    );
  }

  togglePasswordVisibility(): void {
    this.isPasswordVisible = !this.isPasswordVisible;
  }

  async navigateToRegister(): Promise<void> {
    this.router.navigate(["/auth/register"]);
  }

  async navigateToForgotPassword(): Promise<void> {
    this.router.navigate(["/auth/forgot-password"]);
  }

  private async showAlert(title: string, message: string): Promise<void> {
    const alert = await this.alertController.create({
      header: title,
      message: message,
      buttons: ["OK"],
    });
    await alert.present();
  }
}
```

### 8.2 login.component.html

```html
<ion-header>
  <ion-toolbar color="primary">
    <ion-title>Hospital Familia - Login</ion-title>
  </ion-toolbar>
</ion-header>

<ion-content fullscreen>
  <div class="login-container">
    <div class="logo-section">
      <img
        src="assets/images/hospital-logo.png"
        alt="Hospital Familia"
        class="logo"
      />
      <h1>Hospital Familia</h1>
      <p>Gestión Integral de Salud</p>
    </div>

    <form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
      <ion-card>
        <ion-card-content>
          <!-- Email -->
          <ion-item>
            <ion-label position="floating">Email</ion-label>
            <ion-input type="email" formControlName="email" required>
            </ion-input>
          </ion-item>
          <ion-text color="danger" *ngIf="email?.invalid && email?.touched">
            <small>Email válido requerido</small>
          </ion-text>

          <!-- Password -->
          <ion-item>
            <ion-label position="floating">Contraseña</ion-label>
            <ion-input
              [type]="isPasswordVisible ? 'text' : 'password'"
              formControlName="password"
              required
            >
            </ion-input>
            <ion-button
              slot="end"
              fill="clear"
              (click)="togglePasswordVisibility()"
            >
              <ion-icon [name]="isPasswordVisible ? 'eye' : 'eye-off'">
              </ion-icon>
            </ion-button>
          </ion-item>
          <ion-text
            color="danger"
            *ngIf="password?.invalid && password?.touched"
          >
            <small>Contraseña requerida (mínimo 8 caracteres)</small>
          </ion-text>

          <!-- Forgot Password Link -->
          <ion-button
            fill="clear"
            size="small"
            (click)="navigateToForgotPassword()"
            class="forgot-password-link"
          >
            ¿Olvidaste tu contraseña?
          </ion-button>

          <!-- Login Button -->
          <ion-button
            expand="block"
            type="submit"
            color="primary"
            [disabled]="loginForm.invalid || isLoading"
            class="login-button"
          >
            <span *ngIf="!isLoading">Iniciar Sesión</span>
            <ion-spinner *ngIf="isLoading" name="crescent"></ion-spinner>
          </ion-button>

          <!-- Register Link -->
          <ion-text class="register-text">
            ¿No tienes cuenta?
            <ion-button
              fill="clear"
              size="small"
              (click)="navigateToRegister()"
              color="primary"
            >
              Registrate aquí
            </ion-button>
          </ion-text>
        </ion-card-content>
      </ion-card>
    </form>
  </div>
</ion-content>
```

---

## 9. Environment Configuration

### 9.1 environment.ts

```typescript
export const environment = {
  production: false,
  apiUrl: "http://localhost:8080/api",
  appName: "Hospital Familia",
  version: "1.0.0",
  logLevel: "debug",
};
```

### 9.2 environment.prod.ts

```typescript
export const environment = {
  production: true,
  apiUrl: "https://api.hospitalfamilia.com/api",
  appName: "Hospital Familia",
  version: "1.0.0",
  logLevel: "error",
};
```

---

## 10. Directives

### 10.1 has-permission.directive.ts

```typescript
import {
  Directive,
  Input,
  TemplateRef,
  ViewContainerRef,
  OnInit,
} from "@angular/core";
import { AuthService } from "@core/services/auth.service";

@Directive({
  selector: "[appHasPermission]",
})
export class HasPermissionDirective implements OnInit {
  @Input() appHasPermission: string;

  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef,
    private authService: AuthService,
  ) {
    this.appHasPermission = "";
  }

  ngOnInit(): void {
    if (this.authService.hasPermission(this.appHasPermission)) {
      this.viewContainer.createEmbeddedView(this.templateRef);
    } else {
      this.viewContainer.clear();
    }
  }
}
```

---

## 11. Plan de Implementación

### Semana 1-2: Estructura Base y Módulos Core

- [ ] Crear estructura de carpetas
- [ ] Implementar servicios core (Auth, Storage, HTTP)
- [ ] Crear modelos e interfaces
- [ ] Configurar interceptores

### Semana 2-3: Auth Module

- [ ] Implementar componentes de login/register
- [ ] Guards y protección de rutas
- [ ] Validaciones de formularios
- [ ] Integración con API backend

### Semana 3-4: Patient Module

- [ ] Listar pacientes
- [ ] Detalle de paciente
- [ ] Crear/editar paciente
- [ ] Formularios reactivos

### Semana 4-5: Event & Notification Modules

- [ ] CRUD de eventos médicos
- [ ] Centro de notificaciones
- [ ] Dashboards específicos por rol
- [ ] Tests unitarios

---

## 12. Testing

### Ejemplos de uso con Ionic/Angular:

```bash
# Generar nuevo módulo
ng generate module features/patient --routing

# Generar componente
ng generate component features/auth/pages/login

# Generar servicio
ng generate service core/services/auth

# Ejecutar tests
ng test

# Build para producción
ng build --configuration production

# Deploy a Capacitor (iOS)
ionic capacitor run ios

# Deploy a Capacitor (Android)
ionic capacitor run android
```

---

## Checklist de Completitud

- [ ] Estructura Angular completa
- [ ] Servicios core funcionando
- [ ] Guards y protección de rutas
- [ ] Módulos Auth, Patient, Event, Notification creados
- [ ] Componentes principales desarrollados
- [ ] Formularios reactivos validados
- [ ] Interceptores configurados
- [ ] Tests unitarios pasando
- [ ] Build de producción exitoso
- [ ] Capacitor configurado (iOS/Android)
- [ ] PWA funcionando
- [ ] Documentación de componentes
