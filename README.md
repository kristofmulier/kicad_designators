# kicad_designators

Remember that feeling when you're adding a component in KiCAD, but the number is a total mismatch:

<img width="250" src="https://github.com/kristofmulier/kicad_designators/assets/19362684/2f1de496-a5ad-472a-a357-b2e1e30bf2fe">

Of course this should be capacitor `C8`! But that one already exists somewhere in the schematic... This `kicad_designators.py` script solves that problem. However, let's first have a quick look at the *current solution* in KiCAD. Next we'll go over the *new solution* provided by the python script in this GitHub repo - and how to use it.


## 1. Current solution

You can proceed placing `C44` and then invoke the automatic designator renumbering tool from KiCAD (see 'Annotate Schematic...' in the 'Tools' menu):

<img width="250" src="https://github.com/kristofmulier/kicad_designators/assets/19362684/303b1d87-6496-474a-bf48-cda11b58336b">

That works, but then you lose grip on your designator numbers. The automatic annotator tool from KiCAD renumbers your designators either horizontally OR vertically throughout the whole schematic. You can't finetune it. For this reason, I dislike using this particular method - so I found myself renumbering the designators *manually* oftentimes. So painful!

## 2. New solution (using this script)

Copy the content of `kicad_designators.py` and put it in your KiCAD project. The script assumes that all your `.kicad_sch` and `kicad_pcb` files are in a single directory - preferrably the same as the script is dropped in. Now, backup your KiCAD project.

Seriously. Back up your KiCAD project. If something goes wrong, this script can mess it up beyond repair. Also, close your KiCAD project. It will only get confused if you modify the KiCAD files while it's running!

<img width="250" src="https://github.com/kristofmulier/kicad_designators/assets/19362684/2ea1db79-e75f-4fc9-8bc1-94ba0cb5dbd8">

> Close your KiCAD project, make a backup and ONLY THEN run this script.

Now, run the script with the `-s` or `--show` parameter:

```sh
> python kicad_designators.py -s
```

The script lists all your designators and reports any that are missing:
```
[
    'C1', 'C2', 'C3', 'C4', 'C5', 'C6', 'C7', 'C8', 'C9', 'C10', 'C12',
    'C13', 'C14', 'C15', 'C16', 'C17', 'C18', 'C19', 'C20', 'C21', 'C22',
    'C23', 'C24', 'C25', 'C26', 'C27', 'C28', 'C29', 'C30', 'C31', 'C32',
    'C33', 'C34', 'C35', 'C36', 'C37', 'C38', 'C39', 'C40', 'C41', 'C42',
    'C43', 'C44', 'D1', 'D2', 'F1', 'J1', 'J2', 'J3', 'J4', 'J5', 'J6',
    'J7', 'J8', 'JP1', 'LED1', 'LED2', 'LED3', 'LED4', 'LED5', 'Q1', 'Q2',
    'Q3', 'Q4', 'Q5', 'Q6', 'R1', 'R2', 'R3', 'R4', 'R5', 'R6', 'R7', 'R8',
    'R9', 'R10', 'R11', 'R12', 'R13', 'R14', 'R15', 'R16', 'R17', 'R18', 'R19',
    'R20', 'R21', 'R22', 'R23', 'R24', 'R25', 'R26', 'R27', 'R28', 'R29',
    'R30', 'SW1', 'SW2', 'SW3', 'SW4', 'SW5', 'TP1', 'TP2', 'TP3', 'TP4',
    'TP5', 'TP6', 'TP7', 'TVS1', 'TVS2', 'TVS3', 'TVS4', 'TVS5', 'TVS6',
    'TVS7', 'TVS8', 'TVS9', 'TVS10', 'TVS11', 'TVS12', 'TVS13', 'TVS14',
    'TVS15', 'TVS16', 'TVS17', 'TVS18', 'TVS19', 'TVS20', 'TVS21', 'TVS22',
    'TVS23', 'TVS24', 'TVS25', 'TVS26', 'TVS27', 'TVS28', 'TVS29', 'TVS30',
    'TVS31', 'TVS32', 'TVS33', 'TVS34', 'TVS35', 'U1', 'U2', 'U3', 'U4',
    'XTAL1', 'XTAL2'
]
Total designators found: 149
Warning: Gap found in C series between 'C10' and 'C12'
```

Okay, so apparently designator `C11` is missing. To fix this, we should shift `C12`, `C13`, ... down. Like so:

`C12` => `C11`
`C13` => `C12`
`C14` => `C13`
...

You get the point. To achieve that, invoke the script with the `-d` or `--decrement` parameter and provide `C12` as the argument:

```sh
> python kicad_designators.py -d C12
```

The script will shift all capacitors one number down, starting from `C12`. Now, the gap should be filled!

Okay, but we still need to solve the problem stated at the beginning of the page:

<img width="250" src="https://github.com/kristofmulier/kicad_designators/assets/19362684/2f1de496-a5ad-472a-a357-b2e1e30bf2fe">

To solve this, we should *create* a gap for capacitor `C8`. In other words: the existing `C8` capacitor should shift one number up to `C9`, and push all subsequent capacitors also one level up. This creates a gap for capacitor `C8`. To achieve this, invoke the script with the `-i` or `--increment` parameter and provide `C8` as the argument:

```sh
> python kicad_designators.py -i C8
```

Confirm the operation. Now, to check what happened, run the script again with the `-s` or `--show` parameter:

```sh
> python kicad_designators.py -s
```

You'll get the following warning:

```sh
Warning: Gap found in C series between 'C7' and 'C9'
```

That's exactly what we need! Open KiCAD and add a new capacitor:

<img width="250" src="https://github.com/kristofmulier/kicad_designators/assets/19362684/4d8b7777-12f0-4646-bd47-bdb3270a1ff4">

The new capacitor automatically fills the gap you just created!

## 3. Notes

The `kicad_designators.py` script operates on both the `.kicad_sch` and `kicad_pcb` files. After the operation, they should still be in sync. Contact me if it wouldn't be the case.

If you forgot how to run the script, just run it with the `-h` or `--help` flag:

```sh
> python kicad_designators.py --help
```

