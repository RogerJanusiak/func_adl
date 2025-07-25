# Using .Where()

As discussed on the query structure page the location of .Where() function changes at what level in the data it is applying the cut. The examples on this page are designed in order to build a strong understanding of using .Where() in xAOD queries. 

## Applying pt Cuts 

There are multiple places .Where() can be called to apply a pt cut. The location of .Where() in the query can change the resulting data. This difference is because of the location of .Where() relative to the .Select() functions. The difference is illustrated in the two examples below.

### Apply to All Values

The following code applies the pt cut before any of the values are selected from the jets. This means that pt, eta, and phi all will only be from jets with 30 GeV or higher.

```python
query = FuncADLQueryPHYSLITE()
jets_per_event = (query
                    .Select(lambda e: e.Jets().Where(lambda j: (j.pt() / 1000 > 30)))
                    .Select(lambda jets: {
                            'pt': jets.Select(lambda j: j.pt() / 1000),
                            'eta': jets.Select(lambda j: j.eta()),
                            'phi': jets.Select(lambda j: j.phi()),
                        })
                 )

jet_data_all = get_data(jets_per_event, physlite_ds)
```

### Apply only to pt Values

The following code applies the pt cut only before the pt .Select() operator. This means that only pt values will be from only cut jets, eta and phi will be from all jets.

```python
query = FuncADLQueryPHYSLITE()
jets_per_event = (query
                    .Select(lambda e: e.Jets())
                    .Select(lambda jets: {
                            'pt': jets.Where(lambda j: (j.pt() / 1000 > 30)).Select(lambda j: j.pt() / 1000),
                            'eta': jets.Select(lambda j: j.eta()),
                            'phi': jets.Select(lambda j: j.phi()),
                        })
                 )

jet_data_only_pt = get_data(jets_per_event, physlite_ds)
```

### Comparison

Plotting the Jet pt and Jet eta will demonstrate the difference in these methods.

```{figure} img/where_1.png
:alt: Plot of Jet pt, Shows Values are Same
:width: 60%
:align: center
```

The plot above shows that the Jet pt data is identical from both methods of applying the cut. This is what is expected.

```{figure} img/where_2.png
:alt: Plot of Jet Eta, Shows Values Differ
:width: 60%
:align: center
```

The eta plot above shows that the Jet eta does not match using both methods. The plot makes it clear that the eta values are not getting trimmed when the .Where() is not called before the eta .Select().

## Skimming Events

To skim events .Where() needs to be used on the event level of the query. This takes place before the first .Select(). 

```python

```