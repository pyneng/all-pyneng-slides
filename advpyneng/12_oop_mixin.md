## Примесь (Mixin)

---
### Mixin

Примесь это класс, который реализует какое-то одно ограниченное поведение.

Mixin классы - это, как правило, классы у которых нет данных, но есть методы.
Mixin используются для добавления одних и тех же методов в разные классы.

В Python примеси делаются с помощью классов. Так как в Python нет отдельного
типа для примесей, классам-примесям принято давать имена заканчивающиеся на Mixin.

С одной стороны, то же самое можно сделать с помощью наследования обычных классов,
но не всегда те методы, которые нужны в разных дочерних классах, имеют смысл в родительском.

---
### Mixin

Есть две основные ситуации, в которых используются примеси:

* одному классу надо предоставить классу множество дополнительных возможностей
* один и тот же функционал надо использовать во множестве разных классов

---
### Пример декоратора, который добавляет метод pprint классу

```python
from rich import print as rprint

def _pprint(self, methods=False):
    attrs = vars(self)
    rprint(attrs)
    all_methods = vars(type(self))
    methods_dict = {
        name: method
        for name, method in all_methods.items()
        if not name.startswith("__") and callable(method)
    }
    if methods:
        rprint(methods_dict)


def add_pprint(cls):
    cls.pprint = _pprint
    return cls


@add_pprint
class IPAddress:
    def __init__(self, ip, mask):
        self.ip = ip
        self.mask = mask

    def __repr__(self):
        return f"IPAddress({self.ip}/{self.mask})"

    def bin_mask(self):
        return "1" * self.mask + "0" * (32 - self.mask)
```

---
### Mixin который добавляет метод pprint


```python
from rich import print as rprint

class PprintMixin:
    def pprint(self, methods=False):
        attrs = vars(self)
        rprint(attrs)
        all_methods = vars(type(self))
        methods_dict = {
            name: method
            for name, method in all_methods.items()
            if not name.startswith("__") and callable(method)
        }
        if methods:
            rprint(methods_dict)

class IPAddress(PprintMixin):
    def __init__(self, ip, mask):
        self.ip = ip
        self.mask = mask

    def __repr__(self):
        return f"IPAddress({self.ip}/{self.mask})"

    def bin_mask(self):
        return "1" * self.mask + "0" * (32 - self.mask)
---

## Mixin examples

---
### [Rich](https://github.com/willmcgugan/rich/blob/eb673d1204340738d3084ebc2e4c789a35a4e49b/rich/jupyter.py#L31)

```python
class JupyterMixin:
    """Add to an Rich renderable to make it render in Jupyter notebook."""

    __slots__ = ()

    def _repr_mimebundle_(
        self, include: Iterable[str], exclude: Iterable[str], **kwargs: Any
    ) -> Dict[str, str]:
        console = get_console()
        segments = list(console.render(self, console.options))  # type: ignore
        html = _render_segments(segments)
        text = console._render_buffer(segments)
        data = {"text/plain": text, "text/html": html}
        if include:
            data = {k: v for (k, v) in data.items() if k in include}
        if exclude:
            data = {k: v for (k, v) in data.items() if k not in exclude}
        return data
```


---
### [scrapli](https://github.com/carlmontanari/scrapli/blob/master/scrapli/driver/core/arista_eos/base_driver.py#L56)

```python
class EOSDriverBase:
    # EOSDriverBase Mixin values set in init of sync/async NetworkDriver classes
    privilege_levels: Dict[str, PrivilegeLevel]

    def _create_configuration_session(self, session_name: str) -> None:
        """
        Handle configuration session creation tasks for consistency between sync/async versions
        Args:
            session_name: name of session to register
        Returns:
            None
        Raises:
            ScrapliValueError: if a session of given name already exists
        """
        if session_name in self.privilege_levels.keys():
            msg = (
                f"session name `{session_name}` already registered as a privilege level, chose a "
                "unique session name"
            )
            raise ScrapliValueError(msg)
        sess_prompt = re.escape(session_name[:6])
        pattern = (
            rf"^[a-z0-9.\-@()/: ]{{1,63}}\(config\-s\-{sess_prompt}[a-z0-9_.\-@/:]{{0,32}}\)#\s?$"
        )
        name = session_name
        config_session = PrivilegeLevel(
            pattern=pattern,
            name=name,
            previous_priv="privilege_exec",
            deescalate="end",
            escalate=f"configure session {session_name}",
            escalate_auth=False,
            escalate_prompt="",
        )
        self.privilege_levels[name] = config_session
```

---
### [werkzeug](https://github.com/pallets/werkzeug/blob/1.0.x/src/werkzeug/wrappers/user_agent.py)

```python
class UserAgentMixin(object):
    """Adds a `user_agent` attribute to the request object which
    contains the parsed user agent of the browser that triggered the
    request as a :class:`~werkzeug.useragents.UserAgent` object.
    """

    @cached_property
    def user_agent(self):
        """The current user agent."""
        return UserAgent(self.environ)
```

```python
from werkzeug import BaseRequest


class Request(AcceptMixin, ETagRequestMixin, UserAgentMixin, AuthenticationMixin, BaseRequest):
    pass
```
