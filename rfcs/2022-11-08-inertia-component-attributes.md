- Author: Cristian Herrera <cristian@ocmiwc.com>
- Original Date: 11/08/2022

# Solely using attributes to specify Inertia views
This RFC covers an alternative, more REST-friendly usage of Inertia, one of the main components of our development stack.

Most impactfully, this RFC would stop usages of most Inertia response helpers and instead restore standard JSON/array responses from Laravel controllers.

Instead, an attribute, `#[Inertia('View')]` would be placed on top of the controller. A middleware would then dynamically transform the response prior to the final response being sent according to request headers.

## Motivation
The power of Inertia comes from its ability to bridge the gap between frontend and backend on PHP applications. We are able to render views and pass along any necessary data without the need for additional requests or even handling of request lifecycle events.

This comes at the cost of handicapping the controllers so that their resulting payloads must be accessed using Inertia's JS counterpart. Often this leads to deduplicated code, or even two controllers that handle requests for the same resource, their only difference being the context in which they expect requests to be received in.

Over time this affects growth terribly, and makes it a royal pain to provide third-party developers with access to robust APIs that safely mimic internal APIs used to build the host platform.

A secondary benefit to this would also be improved and less magical return type definitions.

This approach allows us to still leverage the power of Inertia while still enforcing a REST-style request and response payload pattern. Caveats, like potential incompatibilities with lazy loaded props, will be covered later on.

## Design Detail
An implementation similar to what will be explained in this RFC was done for **PayrollGoat** later on in its development and yielded good results. It significantly decreased feature integration time for the mobile app interface. This is additional to other benefits like improved error handling during form fill-out (_Even prior to submission_).

We would begin by designing an attribute that replaces the usage of the `inertia()` helper, or `Inertia` facade:

```php
#[Attribute]
class Inertia
{
    public function __construct(public string $view) { }
}
```

It would be used in a controller likeso: (_A `diff` is being provided for improved visibility_)

```diff
class LeadController extends Controller
{

-   public function index(LeadRepository $repository): ResponseFactory|Response
+   #[Inertia('Leads/Index')]  
+   public function index(LeadRepository $repository): Enumerable
    {
-       return inertia('Leads/Index', ['leads' => $repository->list()]);
+       return ['leads' => $repository->list()];
    }
}
```

Then, a middleware would be put in place at any point after the response is initially dispatched by the controller action. This middleware would take care of detecting the presence of the `#[Inertia]` attribute and mutating its response appropriately based on things like request headers for example:

```php
class InertiaResponseTransformerMiddleware
{
    public function handle($request, Closure $next)
    {
        if ($request->inertia()) {
          return $next(inertia($this->getInertiaView(), $request->data));
        }
 
        return $next($request);
    }
    
    protected function getInertiaView(): string
    {
        $reflection = getControllerMethodReflection();
        $attribute = getInertiaAttribtue($reflection);
        
        return $attribute->view;
    }
}
```

Some additional adjustments would need to be made to account for things commonly done in Inertia routes that would no longer be possible, for example,
redirecting users.

Some of these can be solved by adding more parameters to the attribute, for example, when handling redirections after a resource is created:

```php
#[Inertia('Leads/Index', 'leads.show')]
```

We could then **expect** the payload to have an `id` key present at the root level, and then provide that as the route parameter of `leads.show`. Another option is allowing the passing of a closure to calculate the redirect URI.

```php
#[Inertia('Leads/Index', fn ($props) => route('leads.show', ['lead_id' => $props['id']]))]
```

In either case, we could support both usages by just executing different logic based on the type of the passed parameter.

With all this in place, we'd be able to leverage Inertia, but still have full use of our API as a regular REST API.

### Cost and Alternatives
I would not expect this to go beyond 2 - 3 days of solid development. I think it would be worth every second as we've seen just a simple version of it has worked charms in PRG (_This one would have no bugs either! (hopefully)_).

This feature would be mainly used in Phoenix, which is still at its inception stage. Its architecture allows quick refactor of routes by enforcing small controllers only present at the entrypoint package, so I don't expect lengthy refactors either for implementation.

Testability should also not be affected.

Alternatives to this could be making separate controllers or endpoints just for API purposes, or conditionally rendering different payloads, which is what we would be doing with the spec on here either way, just in a more elegant manner.

### Unanswered Questions
--- WIP ---
Most questions will just go around functions executed at the request-level inertia handles via its helpers that would not be available here.
