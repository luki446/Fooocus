# Fooocus

<img src="https://raw.githubusercontent.com/lllyasviel/misc_files/main/202308/fsm2.png" width=100%>

Fooocus is an image generating software.

Fooocus is a rethinking of Stable Diffusion and Midjourney’s designs:

* Learned from Stable Diffusion, the software is offline, open source, and free.

* Learned from Midjourney, the manual tweaking is not needed, and users only need to focus on the prompts and images.

Fooocus has automated [lots of inner optimizations and quality improvements](tech_list). Users can forget everything about technical parameters, and just enjoy the interaction between human and computer to "explore new mediums of thought and expanding the imaginative powers of the human species" `[1]`.

Fooocus has simplified the installation. Between pressing "download" and generating the first image, the number of needed mouse clicks is strictly limited to less than 3. Minimal GPU memory requirement is 4GB (Nvidia).

`[1]` David Holz, 2019.


## Download

### Windows

**[>>> Click here to download <<<](https://github.com/lllyasviel/Fooocus/releases/download/release/Fooocus_win64_1-1-10.7z)**

After you download the file, please uncompress it, and then run the "run.bat".

In the first time you launch the software, it will automatically download models:

1. It will download [sd_xl_base_1.0.safetensors from here](https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0/resolve/main/sd_xl_base_1.0.safetensors) as the file "Fooocus\models\checkpoints\sd_xl_base_1.0.safetensors".
2. It will download [sd_xl_refiner_1.0.safetensors from here](https://huggingface.co/stabilityai/stable-diffusion-xl-refiner-1.0/resolve/main/sd_xl_refiner_1.0.safetensors) as the file "Fooocus\models\checkpoints\sd_xl_refiner_1.0.safetensors".

If you already have these files, you can copy them to the above locations to speed up installation.

### Linux and Mac

Coming soon ...

## List of "Hidden" Tricks
<a name="tech_list"></a>

Below things are already inside the software, and **users do not need to do anything about these**.

Note that some of these tricks are currently (2023 Aug 11) impossible to reproduce in Automatic1111's interface or ComfyUI's node system. You may expect better results from Fooocus than other software even when they use similar models/pipelines.

1. Native refiner swap inside one single k-sampler. The advantage is that now the refiner model can reuse the base model's momentum (or ODE's history parameters) collected from k-sampling to achieve more coherent sampling. In Automatic1111's high-ref fix and ComfyUI's node system, the base model and refiner use two independent k-samplers, which means the momentum is largely wasted, and the sampling continuity is broken. Fooocus uses its own advanced k-diffusion sampling that ensures seamless, native, and continues swap in a refiner setup.
2. Negative ADM guidance. Because the lowest resolution level of XL Base does not have cross attentions, the positive and negative signals for XL's lowest resolution level cannot receive enough contrasts during the CFG sampling, causing the results look a bit plastic or overly smooth in certain cases. Fortunately, since the XL's lowest resolution level is conditioned on image aspect ratios (ADM), we can modify the adm on the positive/negative side to compensate for the lack of CFG contrast in the lowest resolution level.
3. We modified the style templates a bit and added the "cinematic-default".
4. We tested the "sd_xl_offset_example-lora_1.0.safetensors" and it seems that when the lora weight is below 0.5, the results are always better than XL without lora.
5. The parameters of samplers are carefully tuned.
6. Because XL uses positional encoding for generating resolution, images generated by several fixed resolutions look a bit better than that from arbitrary resolutions. This suggests that resolutions in UI may be hard coded for better results.
7. Separated prompts for two different text encoders seem unnecessary. Separated prompts for base model and refiner may work but the effects are random, and we refrain from implement this.
8. DPM family seems well-suited for XL, since XL sometimes generates overly smooth texture but DPM family sometimes generate overly dense detail in texture. Their joint effect looks neutral and appealing to human perception.

## Thanks

The codebase starts from an odd mixture of [Automatic1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui) and [ComfyUI](https://github.com/comfyanonymous/ComfyUI). (And they both use GPL license.)
