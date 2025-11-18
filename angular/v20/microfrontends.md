

### **Microfrontends and Scaling Federated Angular Apps Beyond a Single Repo**



---





#### 💡 Quick Clarification: v2 or v3 of the AdminPortal?



Based on the AdminPortal project’s evolution so far:



* **v1** → Classic Angular (pre-standalone), centralized modules, template-driven UI.

* **v2** → Modernized with *standalone components*, *Signals*, and *NgRx coexistence* (reactive local + global state).

* **v3** → Introduces *Federation*, *Enterprise modularity*, and *deployable microfrontends*.



This next phase is **AdminPortal v3**.

The jump from v2 → v3 isn’t just about code; it’s about *architecture maturity* and *scaling teams*.



To scale successfully, focus on architecture instead of just adding new features.



---







#### 🧩 Article Draft:



# **Microfrontends and Scaling Federated Angular Apps Beyond a Single Repo**



---



#### **Introduction: From Monolith to Modular Suite**



By the time your Angular app grows beyond a few feature areas, you start to feel the pain of scale:



* Build times stretch past 10 minutes

* Teams trip over shared dependencies

* One PR can break ten unrelated features



At this point, moving to microfrontends stops being just a trend and becomes a practical necessity.



With Angular v20, the new **standalone component model**, **federated modules**, and **signal-driven shared libraries** make it easier than ever to scale an app like `AdminPortal` into an enterprise-grade modular suite.



We’ll evolve our **AdminPortal v2** into **AdminPortal v3**, built for distributed teams, independent deploys, and shared design systems.



---



### **1. Federation in Angular: The Big Picture**



At the heart of Angular federation was **Webpack Module Federation**, now fully supported through tools like **Angular Architects Module Federation** and the **Angular CLI Builders** ecosystem.
Angular 20 embraces Module Federation and Native Federation, as two seperate paths. 



This allows us to:



* Split the app into multiple deployable apps (“remotes”)

* Expose standalone components or routes dynamically

* Share libraries and signals without tight coupling



Each team owns its vertical — for example:



| App Name        | Responsibility                 | Example Route |

| --------------- | ------------------------------ | ------------- |

| `admin-shell`   | Core shell, layout, navigation | `/dashboard`  |

| `users-app`     | User management microfrontend  | `/users`      |

| `billing-app`   | Invoicing and reports          | `/billing`    |

| `analytics-app` | Data visualization             | `/analytics`  |



---



### **2. Federated Modules Using Standalone Routes**



Angular 20’s **standalone routes** remove the need for feature modules entirely making federation simpler.



Each remote app exposes a route entry point like this:



```ts

// users.routes.ts

import { Routes } from '@angular/router';

import { provideState } from '@ngrx/store';

import { UserListComponent } from './user-list.component';

import { userReducer } from './state/user.reducer';



export const USERS_ROUTES: Routes = [

  {

    path: '',

    component: UserListComponent,

    providers: [provideState({ users: userReducer })]

  }

];

```



Then the shell loads it dynamically:



```ts

// shell.routes.ts

export const APP_ROUTES: Routes = [

  {

    path: 'users',

    loadChildren: () =>

      loadRemoteModule({

        type: 'module',

        remoteEntry: 'https://users-app.company.com/remoteEntry.js',

        exposedModule: './UsersRoutes'

      }).then(m => m.USERS_ROUTES)

  }

];

```



Each route is self-contained, fully standalone, and still benefits from DI and Signals. No need for shared NgModules or tangled imports.



---



### **3. NgRx Store Segmentation for Federated State**



When splitting state across federated apps, NgRx can easily become monolithic again if not planned carefully.



Angular v20 encourages a **segmented store pattern**:



```ts

// users-app bootstrap

bootstrapApplication(AppComponent, {

  providers: [

    importProvidersFrom(StoreModule.forRoot()),

    provideState({ users: userReducer })

  ]

});

```



Each remote provides its own slice of the store.

The shell can **lazy-connect** to these segments as routes load, without pre-registering reducers upfront.



You can even merge signal-based local state with NgRx global state:



```ts

// Inside a UserComponent

userCount = computed(() => this.store.selectSignal(selectUserCount));

```



Signals bridge the boundary between global NgRx selectors and local reactive UI. No manual subscriptions or boilerplate.



---



### **4. Shared UI Libraries and Design Systems via Signals**



A federated architecture still needs *a unified experience*.

This is where **signal-driven design systems** shine.



The shared design system (`ui-lib`) exposes reactive tokens and themes:



```ts

// theme.signal.ts

export const themeSignal = signal<'light' | 'dark'>('light');



export function toggleTheme() {

  themeSignal.update(v => (v === 'light' ? 'dark' : 'light'));

}

```



Any app, such as users, billing, or analytics, can import this signal:



```ts

@Component({

  standalone: true,

  selector: 'app-toolbar',

  template: `

    <button (click)="toggle()">Toggle Theme</button>

  `

})

export class ToolbarComponent {

  toggle = toggleTheme;

}

```



The state propagates across remotes instantly, without NgRx or event buses.

That’s the new power of cross-app Signals.



---



### **5. Deploying AdminPortal as a Modular Enterprise Suite**



Each microfrontend is built and deployed independently, for example, in AWS:



| Service       | Deployment      | URL                                  |

| ------------- | --------------- | ------------------------------------ |

| `admin-shell` | S3 + CloudFront | `portal.company.com`                 |

| `users-app`   | S3 + CloudFront | `users.company.com/remoteEntry.js`   |

| `billing-app` | S3 + CloudFront | `billing.company.com/remoteEntry.js` |



Your CI/CD pipeline can version and promote each app separately:



* `admin-shell` consumes remote URLs dynamically

* Feature teams deploy independently

* Canary versions can load side-by-side



**Angular 20 + Federation** means your monolith evolves into an ecosystem.



---



### **6. Lessons Learned from AdminPortal v3**



| What Changed                          | Why It Matters                      |

| ------------------------------------- | ----------------------------------- |

| Standalone routes replaced modules    | Simplified federation boundaries    |

| Signals replaced shared services      | Reactive UI state across remotes    |

| NgRx segmented by domain              | Scalable state without coupling     |

| Shared design system built on signals | Unified UX without shared NgModules |

| Independent deployments               | Faster delivery, smaller risk       |



---



### **Conclusion: Scaling the Angular Mindset**



Microfrontends aren’t just an architectural decision; they’re an organizational one.

Angular 20 finally gives us the tools to do this cleanly:



* Standalone everything

* Signal-driven reactivity

* Store segmentation

* Federated routing



The `AdminPortal v3` story isn’t about breaking things apart; it’s about **building a system that scales with your teams**.



---



Next:

1. Create **architecture diagrams** for AdminPortal v3 (showing shell + remotes + signals flow)?

2. Add a **code appendix** with minimal working examples (for `shell`, `users-app`, `shared-lib`)?



Turns this into a mini whitepaper, providing a fantastic side-by-side comparison with the earlier Angular modernization chapters.









