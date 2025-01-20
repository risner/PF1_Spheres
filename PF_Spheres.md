# Documentation

## Coding Documentation

This section documents various things, fields, and tags in use.

### Trade Traditions

A character is permitted an optional trade tradition chosen at the 1st level of a character. Without the tradition, the character handles class skills as normal. With the trade tradition, you lose all class skills (with caveats) and gain \[trade\] talents.

#### About Class Skills

- When you have a Trade Tradition, any existing class skills will be removed from the class at PreLevel/498 ("Remove all class-skills").
- Automatic Class Skills are added to the 1st level class at PreLevel/499 ("Add Trade Tradition class-skills").
      Some classes may have a **TradeClSk.?** tag on BaseClHelp, these will be also be added as **ClassSkill.?**.
- Core forwards **ClassSkill.?** from BaseClHelp to hero at PreLevel/500 ("Component Class: Eval Script #13").
- Core assigns **Helper.ClassSkill** to the skill is assigned at Final/1000 ("Assign Helper.ClassSkill based on ClassSkill tags on Hero")
        Some are assigned at PostLevel/10000, and few at PostLevel/5000.

#### SphOfGuil.Trade

This tag will be assigned to the hero, the trade tradition \(SoGTraTrad component\), and the class. Currently this is done by two scripts:
- The adjustment *pSoGSkForTraTrd* "Assign Trade Rank based on skill points of class” ran at First/50.
- A bootstrap for all Guile classes an archetypes *cTradeTradition* "Link class helper to trade tradition” ran at First/75.

The following scripts depend on this tag being assigned:
- "Compile overlapping skils information” ran at Final/20002 in the thing \(SoGOverlappingSk\).
- Several scripts in any thingid based on the component \(SoGTraTrad\).
    - "Remove all class-skills” ran at PreLevel/498.
    - "Add Trade Tradition class-skills” ran at PreLevel/499.
    - "Add additional free trade talents” ran at First/500.
- "Allow operatives to take a trade tradition” ran at PostAttr/1000 in the mechSoG thing based on the Mechanics component.
- The Vocation tab will show if the hero has the tag.

#### SoGTRank.?

This tag has only two options \(Competent and Adroit\) and is assigned or required by the following scripts:
- Any thing based on the component \(*SoGTraTrad*\) should bootstrap their Adroit talents at First/102 if SoGTRank.Adroit is assigned to the hero.
- Any archetype \(e.g. *arVigInformant*\) should bootstrap *cTradeTradition* and assign their rank via a script by First/50.
- Any class helper \(e.g. *cHelpSGTheor*\) should bootstrap *cTradeTradition* and assign the appropriate rank to the class.
- The component \(SoGTraTrad\) upgrades to Adroit if any class has that rank in the "Handle Adroit” script ran at First/100.
- The adjustment *pSoGSkForTraTrd* script "Assign Trade Rank based on skill points of class” ran at First/50 assigns based on class skill ranks the class provides.

#### SphOfGuil.Class

This is assigned to any class that is consider a Guile class.
- Any archetype \(e.g. *arVigInformant*\) should assign this tag SphOfGuil.Class to the class helper in a script.
- Any class helper \(e.g. *cHelpSGTheor*\) should assign this in the class definition.

There are a few scripts that interact with this tag:
- Assigned to hero by pSoGSkForTraTrd at First/50 "Assign Trade Rank based on skill points of class"
- Assigned to hero by abSoGSkSp, if bootstrapped, makes you Guile at First/500 "Enable skill spheres” if you are not already.
- The adjustment *pSoGFeattTalProg* trades feat progress for a skill talent progression.
- All talent progressions based on the component \(*SoGSkTrain*\) require the character to be a Guile by PostLevel/1000 to calculate talents gained.

#### SphOfGuil.TradeDupe

This tag is assigned when multiple \[trade\] talent grant the same class skill. It is removed when using a skill replacement gained for every two talents. For each replacement, you take a component \(*SoGSkillReplace*\) to manage the replacement. It also is used to grant the class skill bonus increase from +3 to +4 for any class skill duplicated.

### Skill Sphere Progression

#### SoGOLProg.?

This tag has only three options \(Trained, Journeyman and Virtuoso\) and is assigned or required by the following scripts:
- Any archetype \(e.g. *arVigInformant*\) should assign the correct tag to the class modified. 
- Any class helper \(e.g. *cHelpSGTheor*\) should assign the correct tag.
- All talent progressions based on the component \(*SoGSkTrain*\) store the talents gained at each level.

### Operative Modifier

#### SoGOMAttr.?

A Guile character chooses a mental ability (Int, Wis, or Cha) for their operator modifier. Some classes and options may expand this list. There is currently no support for expanding the option. If expansion is needed, support will be added.

### Associated Skills

#### SoGAssocSk.?

This is assigned to the GainSphere talent \(e.g. sogTlSpInvestigat\) to represent associated skills that grant free ranks. When the character has multiple associated skills, they may use whichever is higher for sphere stats such as DC, Range, etc. There is no actual limit to the number of actual associated skills, but the typical maximum is 5. Study’s focused study talent could easily exceed that if used a lot.

The following scripts depend on or manipulate the associated skills:
- The tag is copied from the GainSphere talent to the component *SoGSphere* \(e.g. sogSpInvestigatio\) by "Set Sphere Effective Level” ran at PostAttr/2025.
- All talents require *SoGAssocSk.?*, if applicable, to be assigned when running "Apply free ranks for associated skills SoG” at PostLevel/1002. The tag is also assigned to the skill thing by this script.
- The mechSoG script "Handle competence bonus on associated skill dup” ran at PostLevel/1003 applies competence bonuses for SoM associated skills that are also SoG associated skills.
- Various things assign an Associated Skill with a script named "Assign initial Associated Skill” ran at First/103.

#### SoGTopAsSk.?

This is assigned by "Apply free ranks for associated skills SoG” at PostLevel/1002. It is used by tabs to display the associated skill of the sphere with the highest ranks.

#### ExtRanks.?

This is used to determine if we have applied extra ranks for associated skills previously.

The following scripts interact with this tag:
- The tag is applied to the talent \(*SoGTalent*\) and the hero in "Apply free ranks for associated skills SoG” at PostLevel/1002. If the tag exists, one more is added but the field[skExtRanks] is not modify.
- The script "Mark SoM talents as ExtRanks” in mechSoG runs at PostLevel/1001 to mark relevant SoM talents as having ExtRanks.? tags.

#### About Associated Skill Ranks

- The "Apply free ranks for associated skills SoG” script runs at PostLevel/1002 and assigns *tAssocRanks* if we have a **SoGAssocSk.?** tag.
- User added skill ranks are included in associated skill ranks for the purpose of DC, Range, and anything else using *SphereCPLvBas*.
- Spheres of Power and Might assign *SphereCPLvBas* at PostAttr/2025. This script has no support for Spheres of Guile, so does nothing.
- The "Set Sphere Effective Level” script assigns *SphereCPLvBas* at PostAttr/2025 from *tAssocRanks* to support pheres of Guile.
- The “Skilled Sneak” \(sogTlSkilledSneak\) talent is an example of granting an additional associated skill.

### Skill Leverage

#### SkLeverage.?

This tag is assigned to GainSphere talents \(e.g. sogTlSpInvestigat\) for each unlocked skill with Skill Leverage. The \(resSGSkLeverage\) resource tracks how many skills with Skill Leverage a character can have:
> If you would unlock skill leverage with a skill you already have it unlocked for, you can unlock skill leverage with another skill of your choice.

- For Skill Leverage gained from talents, the "Assign initial Skill Leverage” script should assign the tag to the GainSphere talent by PostLevel/1500.
- Any changes to the Skill Leverage on that GainSphere talent should be ran by “Change Skill Leverage” PostLevel/2000.
- The script “Handle Skill Leverage” runs on GainSphere Talents at PostLevel/3000 to increment the resource’s resMax for each SkLeverage.? Tag, then also assign the corresponding tag to the skill.
- If additional Skill Leverage are needed, a table in the Skill Sphere portal “Skill Leverage” allows you to add a thing called “SoGSKUnlock” to choose a skill. This thing runs a script "Assign initial Skill Leverage” at PostLevel/1500 to add a SkLeverage.? Tag to the skill.
- The Skill Leverage resource \(resSGSkLeverage\) should run a script at PostLevel/4000 to increment resSpent for each skill with at least one SkLeverage.? tag.

### Miscellaneous

#### Helper.FirstLevel

I have avoided using this tag as this is assigned to the first class level at Levels/9000. The code needed to know this much earlier, so the code uses the field value *c1stIndex* in the class *BaseClHelp*.

#### Drawbacks
- Should you need to exempt a talent from a ban, it can be accomplished by assigning **SphOfGuil.DBBanSelEx**.
- The Subterfuge drawback Deft is a good example of an \[alternate start\] drawback. I am currently awaiting clarification on how buy off works with \[alternate start\].

## Known Issues

### Not implemented

- arBanshee, arMercenary, arVigInformant, cSoGBravo, cHelpSGTheor, arLivingBanner, arValiantCham all have unimplemented class features.
- sogTlSpInfiltrati needs Startle created
- sogTlSpBodyCont needs Slip Past created
- SoG Packages are implemented only in the Herbalism sphere, use this as an example to implement other spheres.
- sogTlSpBluster, sogTlSpFaction, sogTlSpNavigation, sogTlSpPerformance, sogSpTlSpellhack are all just base sphere.
- sogTlSpStudy has very limited support.
- There may be talents with required choices with no panellink=“” assigned.
- Validations should be posted if multiple **SoGTRank.?** are assigned to the trade tradition.

### Bugs

- Trade Tradition Aerialist may need support for SoGVarTal1.
- The Artifice has hardcoded settings because packages are not yet implemented.
- Dilettante does not implement the validation (one to be a background and they are all be unique).
- Unorthodox Methods has issues and the code is less than ideal.
- Valiant Champion: Divine Alignment may need a custom version just for Insightful Strike.
- Might: Mass Challenge is "one additional creature, + 1 additional creature
        per 2 points of base attack bonus you possess (minimum 1)." so
        should be +1 ("one additional creature") + 1/2 BAB = 1+5 = +6 for
        me not +5. It's done at PostAttr/10000, so any time before then
        abValue += 1 to fix?

### TODO

#### High Priority

#### Low Priority
- Charming Actor & Outlander trade tradition need special coding for 3 to 5 choices.
- Many things do not have usesource lines.
- Implement SoM's "Auto-increment abValue fields" instead bespoke e.g. Theory Bonus?
- Physiological Focus may delete all SkLeverage.skEscape, use "call SoGMinusOne"?
- Bolstering Rapport needs "add 1 for every 6 ranks to name"
- Have Skill Leverage or Associated Skills use Abbreviated skill names?
- PF_Spheres_DDS_Guile_Editor.dat is not yet ready for user editing, need to add SoG specific things that don't exist in other spheres (Might / Power). Then update the editor.dat to be the new formatting (clean) style.
- Unorthodox Methods has issues. Use SoGTalDesc.Unorthodox tag instead of compare() code?
- Teach "Handle competence bonus on associated skill dup" about ExRankValM & ExRankValG
- Utilitarian tradition may need editor changes to support like DDSUniTrad.
- Need to test this works: As normal, if a trade tradition would grant you a talent you already have, you can instead gain a different talent from the same sphere of your choice (which must be a [utility] talent if the granted talent was one).
- Variant "Skill Expertise for Non-Spheres of Guile Classes" needs a different panel.

### Limitations

- Many of the Expanded Utility talents in Might/Power files are not in the published Might/Power files.
- somForRedTec does not implement the cap on the maximum.

## Scripting Notes

- [[Undocumented Features](https://forums.wolflair.com/threads/undocumented-features.59319/)]
- To add commas to numbers, you can use digitgroup\(\).
- The tag User.NoAutoName prevents the shortname from having the selections added.
- [[Using splice\()](https://forums.wolflair.com/threads/splice-function.50485/)]
- ‹candidate inheritlist="yes”> can be used to inherit the <list> into the <candidate> element.
- Use .setstyle in portals to change the text. For example *lblNormal*, *lblSmall*, *lblTiny* are black, *lblWarning*, *lblWarnSm*, and *lblClsNmW* are red🔴, *lblBright* is purple🟣, *lblSklVers* is also purple🟣 with autosizing, *lblCurType*, *lblSklCls*, *lblSklThCl*, are blue🔵, *lblAuto* and *lblAmmo* are green🟢, *lblDisable*, *lblBigDis*, and *lblRaceOff*, *lblSummDs*, *lblSmallDs*, *lblTinyDs*, *lblLeftDs* are all grey🩶, *lblCnXact* is white \(hard to read on-screen\).
- In position scripts, using .setstyle[] then .sizetofit[] will sometimes “center” if your style used “left”.
- If your thingid has options that, if not chosen, should invalidate the panel then put a panellink=“” on the thing.
- When making a skill have optional other ability scores, you may use SkillOpt.? to allow for an optional alternative skill.
- The Athlete Vocation talent has a couple timing restrictions to trip if changes are made.
- For class features that improve with levels, check out Bravo’s Whim: classcheck = "Bravo"; call LevPreReqs - currently WiP.
- Should probably use this procedure more: ~set our focus to the selected pick; call fTargetFoc; doneif (state.isfocus = 0).
- The *Helper Tags.txt* in the *Docs* folder is provided by [[Daniel](https://discord.com/channels/1152447010595667980/1154846188542111774/1330424781740310539).


# Contributors

Thank you to all the people who have already contributed to PF1_Spheres[[contributors](https://github.com/risner/PF1_Spheres/graphs/contributors)]. At present it is just me, I welcome additional contributors.

# In need of Documentation

- Process Skill Replacements
- Increase Class Skill Bonus +3 -> +4
- Tag all duplicate TradeSkills
- Document SoGVarTals.Adroit makes it Adroit only