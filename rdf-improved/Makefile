test_rdf           = $(wildcard test/rdf/*)
test_trig          = $(patsubst test/rdf/%.xml, test/trig/%.trig,     $(test_rdf))
test_jsonld        = $(patsubst test/rdf/%.xml, test/jsonld/%.jsonld, $(test_rdf))


# IMPORTANT: Modify this line to match the exact path to your instances directory
# You might want to use an absolute path or a relative path from the Makefile location
#nordic_source      = /Users/krista/Development/repos/nordic44/Nordic44/instances
# Alternative: If the Makefile is in the same directory as the instances folder
# nordic_source      = ./instances

##### TODO: make path to github repo instead of local path
# GitHub repository for Nordic44

GITHUB_REPO = https://raw.githubusercontent.com/statnett/Nordic44/main
CACHE_DIR = .cache/Nordic44

nordic_source = $(CACHE_DIR)/Nordic44/instances

# Create cache directory when needed
$(nordic_source):
	@mkdir -p $(nordic_source)
	@echo "Created cache directory: $(nordic_source)"
	@mkdir -p $(nordic_source)/NetworkCode
	@mkdir -p $(nordic_source)/Enterprise
	@mkdir -p $(nordic_source)/Grid

# Rule to download files when needed - adjusted path
$(nordic_source)/%.xml:
	@mkdir -p $(dir $@)
	@echo "Attempting to download: $(GITHUB_REPO)/Nordic44/instances/$(@:$(nordic_source)/%=%)"
	@curl -s $(GITHUB_REPO)/Nordic44/instances/$(@:$(nordic_source)/%=%) -o $@ || \
	  (echo "Failed to download file. Trying alternate path..."; \
	   curl -s $(GITHUB_REPO)/instances/$(@:$(nordic_source)/%=%) -o $@ || \
	   (echo "Both download attempts failed for $(@:$(nordic_source)/%=%)"; exit 1))

# Optional helper 
update-nordic:
	@echo "Updating Nordic44 files from GitHub..."
	@rm -rf $(CACHE_DIR)
	@mkdir -p $(nordic_source)
	@echo "Nordic44 files will be downloaded as needed"

clean-nordic:
	@echo "Cleaning Nordic44 cached files..."
	@rm -rf $(CACHE_DIR)
	@echo "Nordic44 cache cleaned"
########

# On Mac/Linux replace:
# nordic_dirs       != /usr/bin/find $(nordic_source) -type d
# With:
nordic_dirs       = $(shell find $(nordic_source) -type d)

# Modify the target directory to match your project structure
nordic_target      = instances/Nordic44
nordic_target_dirs = $(subst $(nordic_source),$(nordic_target),$(nordic_dirs))
# This line finds all XML files, excluding specific files
# You may want to adjust the ignore list based on your specific requirements
nordic_ignore      = CDPSM_2_0/Nordic44_CPSM_01_MF.xml CDPSM_2_0/Nordic44_03_inc.xml CGMES_2_4/Nordic44_CGM_36f_MF.xml CGMES_2_4/Nordic44_CGM_38_CO.xml
nordic_ignore2     = $(patsubst %, $(nordic_source)/%, $(nordic_ignore))

# Recursively find XML files, excluding ignored files
nordic_rdf = $(filter-out $(nordic_ignore2), $(shell find $(nordic_source) -name "*.xml"))
# Changed from this:
#nordic_trig        = $(subst .xml,.trig, $(subst $(nordic_source),$(nordic_target), $(nordic_rdf)))
# COMMENT: Convert XML paths to TRIG paths
nordic_trig        = $(patsubst $(nordic_source)/%.xml,$(nordic_target)/%.trig,$(nordic_rdf))
# Add these targets to help diagnose path and file issues
debug-nordic:
	@echo "Nordic source directory: $(nordic_source)"
	@echo "Directory contents:"
	@ls -l $(nordic_source)
	@echo "Number of files:"
	@ls $(nordic_source) | wc -l

debug-nordic-files:
	@echo "Total XML files found:"
	@echo $(words $(nordic_rdf))
	@echo "First few XML files:"
	@for file in $(wordlist 1,10,$(nordic_rdf)); do echo $$file; done


	

entsoe_source      = ../../../CGMES-SHACL/ENTSO-E_Test_Configurations_v3.0.2/v3.0
# On Mac/Linux replace:
# entsoe_dirs       != /usr/bin/find $(entsoe_source) -type d
# With:
entsoe_dirs       = $(shell find $(entsoe_source) -type d)

entsoe_target      = instances/ENTSO-E_Test_Configurations_v3.0.2
entsoe_target_dirs = $(subst $(entsoe_source),$(entsoe_target),$(entsoe_dirs))
entsoe_rdf         = $(wildcard $(entsoe_source)/*/*/*.xml) $(wildcard $(entsoe_source)/*/*/*/*.xml)
entsoe_trig        = $(subst .xml,.trig, $(subst $(entsoe_source),$(entsoe_target), $(entsoe_rdf)))

multiplied_source  = ../../instance-multiplied
multiplied_target  = instances/multiplied
multiplied_rdf     = $(wildcard $(multiplied_source)/*.xml)
multiplied_trig    = $(subst .xml,.trig, $(subst $(multiplied_source),$(multiplied_target), $(multiplied_rdf)))

zip                = ../../instance-zipped

all:
	@echo targets: context, dirs, zips, test, jsonld, nordic, entsoe, multiplied, rm-test, rm-jsonld, rm-nordic, rm-entsoe, rm-multiplied, clean

context: cim-context-old.jsonld cim-context-new.jsonld
cim-context-%.jsonld: cim-context-%.txt cim-context-common.txt
	cat $^ > $@

dirs:
	-mkdir instances $(nordic_target) $(entsoe_target) $(multiplied_target) $(nordic_target_dirs) $(entsoe_target_dirs)
	touch $(patsubst %, %/.gitkeep, $(multiplied_target) $(nordic_target_dirs) $(entsoe_target_dirs))

zips:
	zip -r -m $(patsubst instances/%, $(zip)/%.zip, $(nordic_target))     $(nordic_target)     -x "*/.gitkeep"
	zip -r -m $(patsubst instances/%, $(zip)/%.zip, $(entsoe_target))     $(entsoe_target)	   -x "*/.gitkeep"
	zip -r -m $(patsubst instances/%, $(zip)/%.zip, $(multiplied_target)) $(multiplied_target) -x "*/.gitkeep"

clean:
	find instances test -type f -size 0 -exec rm {} \;

fix-datatypes-and-model.ru: fix-datatypes-old.ru fix-model.ru
	cat $^ > $@

test: $(test_trig)
rm-test:
	rm -f $(test_trig)
#test/trig/%.trig: test/rdf/%.xml | cim-trig.pl fix-datatypes-and-model.ru
#	perl cim-trig.pl -r $^ > temp.trig
# On Mac/Linux Replace:
#   update.bat --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@

# With:
#	./update --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@

#TODO: where is update??
test/trig/%.trig: test/rdf/%.xml | cim-trig.pl
	perl cim-trig.pl -r $< > $@
# Or if it's a shell script
#	bash update --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@
#		update.bat --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@

		rm temp.trig

jsonld: $(test_jsonld)
rm-jsonld:
	rm -f $(test_jsonld)
test/jsonld/%.jsonld: test/trig/%.trig
# On Mac/Linux Replace:
#	riot.bat -formatted jsonld $^ | jsonld compact -c https://rawgit2.com/Sveino/Inst4CIM-KG/develop/rdf-improved/cim-context-old.jsonld > $@

#With:
#	riot -formatted jsonld $^ | jsonld compact -c https://rawgit2.com/Sveino/Inst4CIM-KG/develop/rdf-improved/cim-context-old.jsonld > $@
	riot -formatted jsonld $< > $@
nordic: $(nordic_trig)
rm-nordic:
	rm -f $(nordic_trig)
$(nordic_target)/%.trig: $(nordic_source)/%.xml | cim-trig.pl fix-datatypes-and-model.ru
	perl cim-trig.pl -r $^ > temp.trig
# On Mac/Linux Replace:
#	update.bat --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@

#With:
	update --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@
	rm temp.trig

entsoe: $(entsoe_trig)
rm-entsoe:
	rm -f $(entsoe_trig)
$(entsoe_target)/%.trig: $(entsoe_source)/%.xml | cim-trig.pl fix-datatypes-and-model.ru
	perl cim-trig.pl -r $^ > temp.trig
# On Mac/Linux Replace:
#	update.bat --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@

#With:
	update --update=fix-datatypes-and-model.ru --data=temp.trig --dump > $@

	rm temp.trig

multiplied: $(multiplied_trig)
rm-multiplied:
	rm -f $(multiplied_trig)
# update.bat cannot fit in my RAM for such large files
$(multiplied_target)/%.trig: $(multiplied_source)/%.xml | cim-trig.pl
	perl cim-trig.pl -r $^ > $@
