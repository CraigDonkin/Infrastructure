# Domain Trusts

- Relationship between two domains or forests 

- Allows users of one domain or forest to access resources i nthe other domain or forest

- Types of trust

  - Automatic
    - Parent/Child
  - Established
    - Forest/External

- Trusted Domain Object represents the trust relationship 

- Trust Directions

  - One-Way
    - Users in the trusted domain can access resources in the trusting domain but not the reverse
  - Two-Way Trust
    - Users of both domains can access resources in the other domain 

- Transitive Trust
  - IF A = B & b = C then A & C have bidirectional relationship 

- Non Transitive

  - Default trust between two domains in different forests when forests don't have a trust relationship

- Domain Trusts

  - Default/Automatic
    - Parent-Child Trust 
      - created automatically between the new domain and the domain that precedes it in the namespace hierarchy
      - Always two-way transitive 
    - Tree-Root Trust
      - Created automatically between a new domain that is added to a forest
        - Always two-way transitive 
  - Shortcut Trusts
    - Used to reduce access times in complex scenarios
      - One way or two way transitive 
  - External Trusts
    - Between two domains in different forests when forests do not have a trust relationship
      - One way or two way non transitive 

- Forest Trusts

  - Between forest root domain
  - Cannot be extended to a third forest
  - One way or two way transitive or nontransitive 

  