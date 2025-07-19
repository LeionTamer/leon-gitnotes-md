```python
from pydantic import BaseModel

def build_structure_string(cls: type) -> str:
    """Manually builds a string representation of a Pydantic model."""
    if not issubclass(cls, BaseModel):
        return f"'{cls.__name__}' is not a Pydantic BaseModel."

    # Start with the class definition line
    lines = [f"class {cls.__name__}(BaseModel):"]

    # Get the model's fields from the class itself
    fields = cls.model_fields
    if not fields:
        lines.append("    pass")
    else:
        # Add each field with its type annotation
        for field_name, field_info in fields.items():
            # Get the string name of the type annotation
            type_name = getattr(field_info.annotation, '__name__', str(field_info.annotation))
            lines.append(f"    {field_name}: {type_name}")

    return "\n".join(lines)


# ---- Example Usage (works in a notebook) ----

# Define your Pydantic class interactively
class User(BaseModel):
    firstname: str
    surname: str

class Engineer(BaseModel):
    user: User
    skills: list[str]
    years_of_experience: int

# Get and print the reconstructed string
test = build_structure_string(Engineer)
print(test)
```
