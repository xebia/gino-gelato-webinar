---
applyTo: '**'
---

# Gino's Gelato - Copilot Instructions

## Project Overview

This is a full-stack ice cream shop application called "Gino's Gelato" where users can:
- Customize ice cream by selecting containers (cone/cup), up to 3 flavors, and toppings
- Add creations to a shopping cart
- Complete checkout with order processing

**Tech Stack:**
- **Frontend:** React 18 + TypeScript + Vite + Tailwind CSS
- **Backend:** .NET 10 + ASP.NET Core Web API + Entity Framework Core (In-Memory)
- **Styling:** Tailwind CSS with custom gelato theme colors

---

## Architecture & Directory Structure

```
ginos-gelato/
├── client/                 # React frontend (Vite)
│   └── src/
│       ├── components/     # Reusable UI components (organized by feature)
│       ├── contexts/       # React Context providers (CartContext)
│       ├── hooks/          # Custom React hooks
│       ├── pages/          # Route-level page components
│       ├── services/       # API service functions (axios)
│       ├── types/          # TypeScript type definitions
│       └── utils/          # Helper utility functions
└── server/                 # .NET backend
    ├── Controllers/        # API controllers (REST endpoints)
    ├── Data/               # EF Core DbContext
    ├── Models/             # Domain models
    └── Services/           # Business logic services
```

---

## Frontend Conventions (React + TypeScript)

### Component Structure
- Use **functional components** with `React.FC<Props>` typing
- Export components as **default exports**
- Place component-specific types inline or import from `types/index.ts`
- Use destructuring for props with default values

```tsx
interface ComponentProps {
    title: string;
    variant?: 'primary' | 'secondary';
}

const Component: React.FC<ComponentProps> = ({ title, variant = 'primary' }) => {
    return <div>{title}</div>;
};

export default Component;
```

### Type Definitions
- Define shared types in `src/types/index.ts`
- Use `type` keyword for object shapes (not `interface` for consistency)
- Domain types: `Flavor`, `Topping`, `IceCream`, `Order`

```typescript
export type IceCream = {
  id?: number;
  container: 'cone' | 'cup';
  flavors: Flavor[];
  toppings: Topping[];
  price?: number;
};
```

### State Management
- Use **React Context** for global state (CartContext)
- Access cart via `useCart()` hook from `contexts/CartContext`
- Local component state with `useState` hook

### Styling with Tailwind CSS
- Use Tailwind utility classes directly in JSX
- Custom theme colors under `gelato-*` namespace: `gelato-pink`, `gelato-blue`, `gelato-yellow`, `gelato-orange`, `gelato-cream`, `gelato-chocolate`, `gelato-berry`
- Custom fonts: `font-poppins`, `font-fredoka`, `font-roboto`
- Custom animations: `animate-float`, `animate-bounce-slow`
- Prefer gradient backgrounds: `bg-gradient-to-r from-pink-500 to-orange-400`

### API Services
- API calls live in `src/services/api.ts`
- Use **axios** for HTTP requests
- Base URL: `http://localhost:5000/api`
- Handle errors with try/catch and console.error

### Routing
- Use **react-router-dom v6** with `<Routes>` and `<Route>`
- Pages: `/` (Home), `/builder` (Builder), `/cart` (Cart), `/checkout` (Checkout)

---

## Backend Conventions (.NET)

### Project Structure
- Target Framework: **.NET 10**
- Namespace: `GinosGelato`
- Use **nullable reference types** (enabled)
- Use **implicit usings** (enabled)

### Controllers
- Inherit from `ControllerBase`
- Apply `[ApiController]` and `[Route("api/[controller]")]` attributes
- Use **constructor injection** for services
- Return `ActionResult<T>` for type safety
- Use async/await with `Task<ActionResult<T>>`

```csharp
[Route("api/[controller]")]
[ApiController]
public class OrdersController : ControllerBase
{
    private readonly OrderService _orderService;

    public OrdersController(OrderService orderService)
    {
        _orderService = orderService;
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<Order>> GetOrder(int id)
    {
        var order = await _orderService.GetOrderByIdAsync(id);
        if (order == null) return NotFound();
        return order;
    }
}
```

### Models
- Use **auto-implemented properties** with initializers
- Initialize collections to empty lists: `= new List<T>()`
- Initialize strings to `string.Empty`

```csharp
public class Order
{
    public int Id { get; set; }
    public string CustomerName { get; set; } = string.Empty;
    public List<IceCream> IceCreams { get; set; } = new List<IceCream>();
    public decimal TotalPrice { get; set; }
    public DateTime OrderDate { get; set; }
}
```

### Services
- Place business logic in `Services/` folder
- Register as **Scoped** in DI: `builder.Services.AddScoped<ServiceName>()`
- Use async methods with `Async` suffix
- Inject `ApplicationDbContext` for data access

### Database
- Using **EF Core In-Memory** database for development
- DbContext: `ApplicationDbContext` in `Data/` folder
- Configure in Program.cs: `options.UseInMemoryDatabase("GinosGelatoDb")`

### CORS Configuration
- Allowed origins: `http://localhost:5173`, `https://localhost:5173` (Vite dev server)
- Allow any header and method

---

## Pricing Logic

Consistent across frontend and backend:
- **Container:** Cone = $2.50, Cup = $3.00
- **Flavors:** $2.00 per flavor (max 3)
- **Toppings:** Variable price per topping (default $0.50)

---

## Code Style Guidelines

### TypeScript/React
- Use **ES2020** target with ESNext modules
- Enable **strict** mode
- Path alias: `@/*` maps to `src/*`
- No unused locals/parameters (enforced by tsconfig)

### C#
- Use **file-scoped namespaces** where possible
- Prefer expression-bodied members for simple getters
- Use **primary constructors** for simple DI (if preferred)
- Follow Microsoft naming conventions (PascalCase for public members)

---

## Running the Application

### Prerequisites
- Node.js 14+
- .NET SDK 10.0

### Commands
```bash
# Frontend (from client/)
npm install
npm run dev         # Starts on http://localhost:5173

# Backend (from server/)
dotnet restore
dotnet run          # Starts on http://localhost:5000
```

### VS Code Tasks (Available)
- `Start Frontend (React)` - Runs Vite dev server
- `Start Backend (.NET)` - Runs .NET server
- `🚀 Start Full Application` - Starts both in sequence

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/flavors` | Get all available flavors |
| GET | `/api/toppings` | Get all available toppings |
| GET | `/api/orders` | Get all orders |
| GET | `/api/orders/{id}` | Get order by ID |
| POST | `/api/orders` | Create new order |

---

## When Adding New Features

### New React Component
1. Create in appropriate `components/` subfolder
2. Use `React.FC<Props>` pattern
3. Apply Tailwind classes with gelato theme
4. Export as default

### New API Endpoint
1. Add method to existing controller or create new controller
2. Create/update model in `Models/`
3. Add business logic to `Services/`
4. Register new services in `Program.cs`

### New Type
1. Add to `client/src/types/index.ts`
2. Mirror in `server/Models/` if needed

---

## Testing Notes

- Frontend: ESLint for linting (`npm run lint`)
- Backend: Swagger UI available at `/swagger` in development
- No formal test suites configured yet

---

## Common Patterns

### Adding to Cart
```tsx
const { addToCart } = useCart();
const iceCream: IceCream = {
    container: 'cone',
    flavors: selectedFlavors,
    toppings: selectedToppings,
};
addToCart(iceCream);
```

### Fetching Data
```tsx
import { getFlavors } from '../services/api';

useEffect(() => {
    const loadFlavors = async () => {
        const data = await getFlavors();
        setFlavors(data);
    };
    loadFlavors();
}, []);
```

### Creating Controller Action
```csharp
[HttpPost]
public async Task<ActionResult<Order>> CreateOrder(Order order)
{
    var created = await _orderService.CreateOrderAsync(order.IceCreams);
    return CreatedAtAction(nameof(GetOrder), new { id = created.Id }, created);
}
```
