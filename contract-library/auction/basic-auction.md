# Basic

{% code title="auction\_basic.arl" %}
```ocaml
archetype auction

variable max_bid : tez = 0tz

asset bid identified by incumbent {
  incumbent : address;
  value : tez;
}

variable deadline : date = 2019-01-01T00:00:00


action place_bid () {

  require {
    c1 : now < deadline
  }

  effect {
    bid.add({ incumbent = caller; value = transferred });
    max_bid := max (transferred, max_bid)
  }
}


(* Users need to exhibit proof they are not the winner to reclaim their bid *)
action reclaim () {

  require {
    c2 : now < deadline;
    c3 : bid.get(caller).value < max_bid;
  }

  effect {
    transfer bid.get(caller).value to caller
  }
}

specification {
  contract invariant s1 {
    forall b in bid, balance > b.value
  }
}

```
{% endcode %}

