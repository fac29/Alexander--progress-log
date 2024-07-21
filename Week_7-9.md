# Assessment

## Week 7

### 1.1 Create and Run a .NET Project

I set up the .NET project for our recipe directory application, initializing a new ASP.NET project and configuring it to serve as the backend for our application.

```CSharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();

```

 ### 1.2 Learn C# Basics

I have been familiarizing myself with C# syntax and concepts, focusing on object-oriented programming principles. Here is an example class representing a user in our application:

```CSharp
public class User
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
    public List<RecipeCollection> RecipeCollections { get; set; }
}
```

### 1.3 Test ASP.NET Project with Swagger

I set up Swagger to test our endpoints, ensuring that we can interact with the API effectively.

```CSharp
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "Recipe Directory API", Version = "v1" });
});

```

### 1.4 Design-Driven Development: Creating Models

We followed a design-driven development approach, starting with the creation of models that represent our data structure for the recipe directory. Here are some of the key models we designed:

#### Recipe Model
```csharp
public class Recipe
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Description { get; set; }
    public User Creator { get; set; }
    public List<Ingredient> Ingredients { get; set; }
    public List<RecipeCollection> Collections { get; set; }
}
```

### 1.5 Implementing Initial Endpoints

We implemented initial endpoints for creating and managing users, recipes, ingredients, and collections. Here is an example endpoint for adding a new recipe:

```csharp
[ApiController]
[Route("[controller]")]
public class RecipesController : ControllerBase
{
    private readonly RecipeContext _context;

    public RecipesController(RecipeContext context)
    {
        _context = context;
    }

    [HttpPost("add")]
    public async Task<IActionResult> AddRecipe(Recipe recipe)
    {
        _context.Recipes.Add(recipe);
        await _context.SaveChangesAsync();
        return Ok(recipe);
    }
}
```

### 1.6 Preparing for Database Integration

While the database setup will be done later, we prepared our context class to ensure a smooth integration with SQLite.

```csharp
public class RecipeContext : DbContext
{
    public RecipeContext(DbContextOptions<RecipeContext> options) : base(options) { }

    public DbSet<User> Users { get; set; }
    public DbSet<Recipe> Recipes { get; set; }
    public DbSet<Ingredient> Ingredients { get; set; }
    public DbSet<RecipeCollection> RecipeCollections { get; set; }
}
```

## Week 8

### 1.1 Implementing Dependency Injection

We implemented dependency injection to improve the flexibility and testability of our code. Here's an example from our `Program.cs`:

```csharp
builder.Services.AddInfrastructure(builder.Configuration);
builder.Services.AddDbContext<CookWithWhatDbContext>(options => 
    options.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection")));
```
This setup allows us to inject the database context and other services into our controllers and classes.

### 1.2 Creating and Using Interfaces
We created interfaces to define contracts for our classes, promoting loose coupling. Here's an example of our IRecipe interface:
```csharp
public interface IRecipe 
{
    int Id { get; }
    string Title { get; set; }
    string Description { get; set; }
    string Image { get; set; }
    string Instructions { get; set; }
}
```
### 1.3 Implementing Repository Pattern
We started implementing the repository pattern to abstract data access. Here's a snippet from our RecipeRepository:
```csharp
public class RecipeRepository : IRecipe
{
    private readonly CookWithWhatDbContext _context = default!;

    public async Task<IEnumerable<IRecipe>> GetAllRecipes()
    {
        return await _context.Recipes.ToListAsync();
    }
}
```
### 1.4 Using Extension Methods for Service Configuration
We utilized extension methods to organize our service configurations, improving the readability of our Program.cs:
```csharp
public static class ServiceCollectionExtensions
{
    public static void AddInfrastructure(this IServiceCollection services, IConfiguration configuration) 
    { 
        var connectionString = configuration.GetConnectionString("CookWithWhatDb");
        services.AddDbContext<CookWithWhatDbContext>(options => options.UseSqlite(connectionString));

        services.AddScoped<IRecipeSeeder, RecipeSeeder>();
    }
}
```
### 1.5 Implementing Data Seeding
We implemented a seeding mechanism to populate our database with initial data:
```csharp
public class RecipeSeeder : IRecipeSeeder
{
    private readonly CookWithWhatDbContext _context;

    public RecipeSeeder(CookWithWhatDbContext context)
    {
        _context = context;
    }

    public async Task Seed()
    {
        if (await _context.Database.CanConnectAsync())
        {
            if (!_context.Recipes.Any())
            {
                var recipes = GetRecipes();
                _context.Recipes.AddRange(recipes);
                await _context.SaveChangesAsync();
            }
        }
    }

    // GetRecipes method implementation...
}
```
### 1.6 Configuring Entity Framework Core
We set up our DbContext to work with SQLite and configured our entity relationships:
```csharp
public class CookWithWhatDbContext : DbContext
{
    public CookWithWhatDbContext(DbContextOptions<CookWithWhatDbContext> options) : base(options) { }

    public DbSet<Users> Users { get; set; }
    public DbSet<Recipes> Recipes { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=../CookWithWhat.Infrastructure/Persistence/cookwithwhat.db");
    }

    // Additional configurations...
}
```

## Week 9

### 1.1 Setting Up GitHub Actions for Continuous Deployment

We implemented a CI/CD pipeline using GitHub Actions to automate our deployment process to AWS EC2. Here's an overview of our workflow:
This workflow triggers on pushes and pull requests to the 'AlexTemp' branch, setting up the environment for our .NET application.
```yaml
name: .NET

on:
  push:
    branches: ['AlexTemp']
  pull_request:
    branches: ['AlexTemp']

jobs:
  deployment:
    environment: 'Production'
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: 8.0.x
      # ... (build and publish steps)
```

### 1.2 Configuring AWS Credentials

We securely stored our AWS credentials as GitHub Secrets and used them in our workflow:
```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v1
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: eu-west-2
```
![image](https://github.com/user-attachments/assets/da0c2e46-6fa3-4ff4-83cf-9978d19371e0)

### 1.3 Managing EC2 Instances
We implemented logic to check for existing EC2 instances and create new ones if necessary:

```yaml
- name: Check if EC2 instance exists
  id: check_ec2
  run: |
    INSTANCE_ID=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=CWW" "Name=instance-state-name,Values=running"
         --query "Reservations[].Instances[?State.Name=='running'].InstanceId" --output text)
    echo "instance_id=$INSTANCE_ID" >> $GITHUB_OUTPUT

- name: Create EC2 instance if not exists
  if: steps.check_ec2.outputs.instance_id == ''
  id: create_ec2
  run: |
    INSTANCE_ID=$(aws ec2 run-instances \
        --image-id ami-026b2ae0ba2773e0a \
        --instance-type t2.micro \
        --key-name CWW \
        --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=CWW}]' \
        --query 'Instances[0].InstanceId' \
        --output text)
    echo "Created new EC2 instance with ID: $INSTANCE_ID"
    echo "instance_id=$INSTANCE_ID" >> $GITHUB_OUTPUT
```
This approach ensures we always have a running instance for deployment.

### 1.4 Deploying the Application
We used SSH to copy our application files to the EC2 instance and configure it:
```yaml
- name: Copy files to EC2
  uses: easingthemes/ssh-deploy@main
  with:
    SSH_PRIVATE_KEY: ${{ secrets.SSH_KEY }}
    REMOTE_HOST: ${{ steps.get_ip.outputs.public_ip }}
    REMOTE_USER: ec2-user
    SOURCE: "./out/"
    TARGET: "/home/ec2-user/app"

- name: Configure EC2 instance
  uses: appleboy/ssh-action@master
  with:
    host: ${{ steps.get_ip.outputs.public_ip }}
    username: ec2-user
    key: ${{ secrets.SSH_KEY }}
    script: |
      # Installation and configuration steps
      # ...
      # Start the application
      nohup ./CookWithWhat.API > /home/ec2-user/app.log 2>&1 &
```
This step copies our built application to the EC2 instance and starts it as a background process.
### 1.5 Setting Up Nginx as a Reverse Proxy
We configured Nginx on our EC2 instance to act as a reverse proxy for our application:
```yaml
server {
    listen 80;
    server_name ${{ steps.get_ip.outputs.public_ip }};

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
This configuration allows our application to be accessible via HTTP on port 80.

### 1.6 Implementing Security Best Practices
We used GitHub Secrets to store sensitive information:

* AWS_ACCESS_KEY_ID
* AWS_SECRET_ACCESS_KEY
* SSH_KEY

These secrets are used securely within our GitHub Actions workflow without exposing them in our code.

![image](https://github.com/user-attachments/assets/ddf7788a-f1d2-4b77-ae51-5edc531ecf39)

### 1.7 Creating a Dedicated AWS IAM User
We created a dedicated IAM user 'CookWithWhatUser' with specific permissions:

AmazonEC2FullAccess
AmazonVPCReadOnlyAccess

This follows the principle of least privilege, giving our deployment process only the necessary permissions.







 ### 2. Show an example of some of the learning outcomes you have struggled with and/or would like to re-visit.
> [**Learning outcome...**]  
> [your evidence here]



















## Feedback (For CF's)
> [**Course Facilitator name**]  
> [*What went well*]  
> [*Even better if*]
