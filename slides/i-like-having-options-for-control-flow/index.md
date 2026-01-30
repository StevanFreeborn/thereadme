---
title: A Trick for Designing Friendly APIs in Go
theme: default
layout: center
class: text-center
fonts:
  sans: "CaskaydiaCove Nerd Font Mono"
---

# Why I love control flow in C#

---
layout: center
class: text-center
---

# The false dichotomy

<div class="grid grid-cols-2 gap-12 mt-16 text-left">
    <v-click>
        <div class="border-l-4 border-red-500 pl-6">
            <h3 class="text-red-400 text-2xl font-bold mb-2">Team "Never Throw"</h3>
            <p class="text-gray-400">"Exceptions are expensive! Treat errors as values! Not every failure is exceptional!"</p>
        </div>
    </v-click>
    <v-click>
        <div class="border-l-4 border-blue-500 pl-6">
            <h3 class="text-blue-400 text-2xl font-bold mb-2">Team "Always Throw"</h3>
            <p class="text-gray-400">"Results are noisy! Just throw and let the middleware handle it! Keep the code clean!"</p>
        </div>
    </v-click>
</div>
<v-click>
    <div class="mt-16 text-2xl text-green-400">
    > Why not both?
    </div>
</v-click>

---
layout: center
---

# The sweet spot

<div class="grid grid-cols-4 gap-4 mt-8 h-full">
    <v-click>
        <div class="bg-gray-800 p-4 rounded border-t-4 border-red-500">
            <div class="font-bold mb-2 text-center">Global Handler</div>
            <div class="text-sm text-center">Prevent crashes</div>
        </div>
    </v-click>
    <v-click>
        <div class="bg-gray-800 p-4 rounded border-t-4 border-green-500">
            <div class="font-bold mb-2 text-center">Controller</div>
            <div class="text-sm text-center">Matches result</div>
        </div>
    </v-click>
    <v-click>
        <div class="bg-gray-800 p-4 rounded border-t-4 border-blue-500">
            <div class="font-bold mb-2 text-center">Service Layer</div>
            <div class="text-sm text-center">Returns results</div>
        </div>
    </v-click>
    <v-click>
        <div class="bg-gray-800 p-4 rounded border-t-4 border-purple-500">
            <div class="font-bold mb-2 text-center">Deep / Domain</div>
            <div class="text-sm text-center">Throws exceptions</div>
        </div>
    </v-click>
</div>

---
layout: center
---

# An example in a web API

---
layout: center
---

# Use exceptions for context agnostic code

```csharp
public async Task<User?> GetUserAsync(int userId)
{
    // NOTE: EF Core might throw exceptions for connection issues, etc.
    var user = await _dbContext.Users.FindAsync(userId);

    // NOTE: Domain logic might throw exceptions for business rules
    if (user?.IsBanned)
    {
        throw new UserBannedException(userId);
    }

    return user;
}
```

---

# Service layer returns results

```csharp
public async Task<Result<User>> GetUserAsync(int userId)
{
    try
    {
        var user = await _userService.GetUserAsync(userId);

        if (user is null)
        {
            return Result.Failure<User>(new UserNotFoundException(userId));
        }

        return Result.Success(user);
    }
    catch (UserBannedException ex)
    {
        return Result.Failure<User>(ex);
    }
}
```

---

# Match results in the controller

```csharp
public static async Task<IResult> GetUserHandler(int userId, IUserService _service)
{
    var result = await _service.GetUserAsync(userId);

    return result.Match(
        user => Ok(user),
        error => error switch
        {
            UserNotFoundException => Results.NotFound(),
            UserBannedException => Results.Forbid(),
            _ => Results.InternalServerError()
        }
    );
}
```

---

# Global handler for unexpected errors

```csharp
public async ValueTask<bool> TryHandleAsync(
    HttpContext httpContext,
    Exception exception,
    CancellationToken cancellationToken
)
{
    _logger.LogError(
        exception,
        "Exception occurred: {Message}", exception.Message
    );

    httpContext.Response.StatusCode =
        StatusCodes.Status500InternalServerError;

    await httpContext.Response
        .WriteAsync("An unexpected error occurred.", cancellationToken);

    return true;
}
```

---
layout: center
---

# C# gives me the freedom to choose

<ul>
    <v-click>
        <li>Use exceptions when deep in the call stack.</li>
    </v-click>
    <v-click>
        <li>Use result types where business logic is centralized.</li>
    </v-click>
    <v-click>
        <li>Use global handlers for unexpected exceptions and to prevent crashes.</li>
    </v-click>
</ul>
