# Basic

This first example is a toy contract which defines a very basic escrow process.

It is mainly a state machine with 5 states and 4 transitions illustrated in the state diagram:

![](../../.gitbook/assets/escrow_simple2.png)

Note that role values must be provided at declaration for security reason.

{% code title="escrow\_basic.arl" %}
```ocaml
archetype escrow_basic

variable buyer : role  = @tz1KmuhR6P52hw6xs5P69BXJYAURaznhvN1k

variable seller : role = @tz1XJYAURaznhvN1khR6P52hw6xs5P691Kmu

variable oracle : role = @tz15P69BXJYAURaznhvN1k1KmuhR6P52hw6x

variable price : tez = 10tz

(* action deadline *)
variable deadline : date = 2019-06-01T00:00:00

(* state machine *)
states =
 | Created initial
 | Aborted   with { i1 : balance = 0tz; }
 | Confirmed with { i2 : balance = price; }
 | Canceled  with { i3 : balance = 0tz; }
 | Completed with { i4 : balance = 0tz; }

transition abort () {
  called by buyer or seller

  from Created
  to Aborted
}

transition confirm () {
  from Created
  to Confirmed when { balance = price }
}

transition[%onlyonce%] complete () {
  called by oracle

  from Confirmed
  to Completed when { now < deadline }
  with effect {
    transfer price to seller
  }
}

transition[%onlyonce%] cancel () {
  called by oracle

  from Confirmed
  to Canceled
  with effect {
    transfer price to buyer
  }
}

security {
  s1 : only_by_role (transfers, oracle)
}

```
{% endcode %}

