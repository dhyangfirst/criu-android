all-y	+= images $(obj)/magic.py pb.py

proto := $(filter-out images/rpc.proto, $(sort $(wildcard images/*.proto)))
proto-py-modules := $(foreach m,$(proto),$(subst -,_,$(notdir $(m:.proto=_pb2))))

# We don't need rpc_pb2.py here, as it is not related to the images.
# Unfortunately, we can't drop ugly _pb2 suffixes here, because
# some _pb2 files depend on others _pb2 files.
images:
	$(Q) protoc -I=images/ -I=/usr/include/ --python_out=$(obj) $(proto)
.PHONY: images

$(obj)/magic.py: scripts/magic-gen.py criu/include/magic.h
	$(call msg-gen, $@)
	$(Q) $(PYTHON) $^ $@

pb.py: images
	$(Q) echo "# Autogenerated. Do not edit!" > $(obj)/$@
	$(Q) for m in $(filter-out opts_pb2, $(proto-py-modules)); do \
		echo "from .$$m import *" >> $(obj)/$@ ;\
	done
.PHONY: pb.py

cleanup-y	+= $(addprefix $(obj)/,magic.py pb.py *.pyc)
cleanup-y	+= $(call cleanify,$(addprefix $(obj)/,$(addsuffix .py,$(proto-py-modules))))
