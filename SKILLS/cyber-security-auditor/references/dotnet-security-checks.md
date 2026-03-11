# .NET / C# Security Reference Checkliste

## 1. ASP.NET Core Security Middleware

### Pflicht-Middleware (Reihenfolge beachten!)
```csharp
// ✅ Korrekte Middleware-Reihenfolge
app.UseHsts();                    // HTTP Strict Transport Security
app.UseHttpsRedirection();        // HTTP → HTTPS
app.UseStaticFiles();
app.UseRouting();
app.UseAuthentication();          // VOR Authorization!
app.UseAuthorization();
app.UseAntiforgery();            // CSRF-Schutz
```

### Security Headers prüfen
```csharp
// Prüfen ob vorhanden:
// X-Content-Type-Options: nosniff
// X-Frame-Options: DENY
// Content-Security-Policy
// Referrer-Policy
// Permissions-Policy
```

**Risiko wenn fehlend:** Clickjacking, MIME-Sniffing, XSS (Medium–High)

---

## 2. Entity Framework & Dapper – SQL Injection

### Entity Framework (sicher by default)
```csharp
// ✅ Sicher – LINQ wird parametrisiert
var user = await _context.Users.Where(u => u.Email == email).FirstAsync();

// ❌ Gefährlich – Raw SQL ohne Parameter
var users = _context.Users.FromSqlRaw($"SELECT * FROM Users WHERE Email = '{email}'");

// ✅ Sicher – Raw SQL MIT Parameter
var users = _context.Users.FromSqlRaw("SELECT * FROM Users WHERE Email = {0}", email);
```

### Dapper
```csharp
// ✅ Sicher
var user = conn.QueryFirst<User>("SELECT * FROM Users WHERE Email = @Email", new { Email = email });

// ❌ Gefährlich
var user = conn.QueryFirst<User>($"SELECT * FROM Users WHERE Email = '{email}'");
```

---

## 3. Identity & JWT-Konfiguration

### JWT-Schwachstellen
```csharp
// ❌ Problematisch: Algorithm = "none" möglich?
// ❌ Schwacher Signing Key (< 256 Bit)
// ❌ Keine Expiry-Validierung

// ✅ Korrekte Konfiguration
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => {
        options.TokenValidationParameters = new TokenValidationParameters {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,          // Expiry prüfen!
            ValidateIssuerSigningKey = true,
            ValidAlgorithms = new[] { "HS256" }, // Algorithm whitelist
            ClockSkew = TimeSpan.FromMinutes(5)
        };
    });
```

### Password Hashing
```csharp
// ❌ NIEMALS: MD5, SHA1, SHA256 für Passwörter
// ✅ Korrekt: BCrypt, Argon2, PBKDF2 (ASP.NET Identity default)
var hasher = new PasswordHasher<ApplicationUser>();
string hash = hasher.HashPassword(user, plainPassword);
```

---

## 4. HttpClient & TLS

```csharp
// ❌ Gefährlich: Zertifikatsvalidierung deaktiviert
handler.ServerCertificateCustomValidationCallback = (_, _, _, _) => true;

// ❌ Gefährlich: Alte TLS-Versionen
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls; // TLS 1.0!

// ✅ Korrekt: TLS 1.2+ (Standard in .NET 6+)
// ServicePointManager.SecurityProtocol wird nicht mehr benötigt – .NET 6+ nutzt OS-Standard

// ✅ HttpClient über DI mit Polly
services.AddHttpClient<MyService>()
    .AddStandardResilienceHandler();
```

---

## 5. Deserialisierung

```csharp
// ❌ KRITISCH: BinaryFormatter ist verboten (seit .NET 5 obsolete)
BinaryFormatter formatter = new BinaryFormatter();
object obj = formatter.Deserialize(stream); // RCE-Risiko!

// ❌ Gefährlich: Newtonsoft mit TypeNameHandling
JsonConvert.DeserializeObject(json, new JsonSerializerSettings {
    TypeNameHandling = TypeNameHandling.All // Gadget-Chain möglich!
});

// ✅ Sicher: System.Text.Json (kein TypeNameHandling by default)
var obj = JsonSerializer.Deserialize<MyType>(json);
```

---

## 6. Secrets & Konfiguration

```csharp
// ❌ Hardcoded Secrets – Pattern zum Suchen:
// "password" =, "secret" =, "apikey" =, "connectionstring" = mit Literal-Wert

// ✅ Development: User Secrets
dotnet user-secrets set "ConnectionStrings:Default" "..."

// ✅ Production: Azure Key Vault
builder.Configuration.AddAzureKeyVault(new Uri(keyVaultUrl), new DefaultAzureCredential());

// ✅ Managed Identity statt Connection String mit Credentials
// Azure SQL: Authentication=Active Directory Managed Identity
```

---

## 7. Logging – Sensitive Data

```csharp
// ❌ Sensitive Daten in Logs
_logger.LogInformation("User login: {Email} {Password}", email, password);
_logger.LogError("Payment failed for card: {CardNumber}", cardNumber);

// ✅ Nur nicht-sensitive Identifikatoren loggen
_logger.LogInformation("User login attempt for UserId: {UserId}", userId);

// Prüfen: Sentry, Application Insights – werden Requests/Bodies geloggt?
```

---

## 8. CORS-Konfiguration

```csharp
// ❌ Zu offen
app.UseCors(b => b.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader());

// ✅ Restriktiv
app.UseCors(b => b
    .WithOrigins("https://myapp.example.com")
    .WithMethods("GET", "POST")
    .WithHeaders("Content-Type", "Authorization")
    .AllowCredentials());
```

---

## 9. CSRF / Anti-Forgery

```csharp
// ✅ Automatisch in Razor Pages / MVC Forms
// Für APIs: SameSite Cookie + Origin-Check
options.Cookie.SameSite = SameSiteMode.Strict;
options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
```

---

## 10. Dependency Check

```bash
# NuGet-Pakete auf bekannte Vulnerabilities prüfen
dotnet list package --vulnerable
dotnet list package --outdated

# OWASP Dependency-Check Integration
dotnet tool install --global dotnet-retire
dotnet retire
```