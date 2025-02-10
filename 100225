import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

# Title of the app
st.title("Thermal Shift Data Analysis")

# Input: Tm D of the protein
protein_tm_d = st.sidebar.number_input("Enter the Tm D of the protein alone:", min_value=0.0, format="%.2f")

# File uploader for the three CSV files
st.sidebar.header("Upload Files")
fluorescence_file = st.sidebar.file_uploader("Upload Fluorescence CSV", type="csv")
derivative_file = st.sidebar.file_uploader("Upload Derivative CSV", type="csv")
metadata_file = st.sidebar.file_uploader("Upload Metadata CSV", type="csv")

# Check if all files are uploaded
if fluorescence_file and derivative_file and metadata_file:
    # Read CSV files
    fluorescence_data = pd.read_csv(fluorescence_file)
    derivative_data = pd.read_csv(derivative_file)
    metadata_data = pd.read_csv(metadata_file)

    # Convert Tm D column to numeric
    metadata_data["Tm D"] = pd.to_numeric(metadata_data["Tm D"], errors="coerce")

    # Validate the structure of the files
    required_columns_fluorescence = {"Well Position", "Temperature", "Fluorescence"}
    required_columns_derivative = {"Well Position", "Temperature", "Derivative"}
    required_columns_metadata = {"Well", "Tm D"}

    if not required_columns_fluorescence.issubset(fluorescence_data.columns):
        st.error("Fluorescence file must contain 'Well Position', 'Temperature', and 'Fluorescence' columns.")
    elif not required_columns_derivative.issubset(derivative_data.columns):
        st.error("Derivative file must contain 'Well Position', 'Temperature', and 'Derivative' columns.")
    elif not required_columns_metadata.issubset(metadata_data.columns):
        st.error("Metadata file must contain 'Well' and 'Tm D' columns.")
    else:
        # Merge data for well selection
        available_wells = sorted(set(fluorescence_data["Well Position"]), key=lambda x: (x[0], int(x[1:])))
        wells_to_plot = st.sidebar.multiselect("Select Wells to Plot (Individual)", options=available_wells)
        wells_to_overlay = st.sidebar.multiselect("Select Wells to Overlay Fluorescence", options=available_wells)

        def save_figure(fig, filename):
            """Save the current figure as a PNG."""
            fig.savefig(filename, dpi=300, bbox_inches="tight")
            st.success(f"Saved plot as {filename}")

        # Individual well plots
        if wells_to_plot:
            st.header("Individual Well Plots")
            for well in wells_to_plot:
                # Filter data for the selected well
                fluorescence_well_data = fluorescence_data[fluorescence_data["Well Position"] == well]
                derivative_well_data = derivative_data[derivative_data["Well Position"] == well]
                metadata_well = metadata_data[metadata_data["Well"] == well]

                # Extract Tm D value
        if not metadata_well.empty:
            tm_d_value = round(float(metadata_well["Tm D"].values[0]), 2)
        else:
            tm_d_value = None

         # Plot Fluorescence
            st.subheader(f"Fluorescence Plot for Well {well}")
            fig, ax = plt.subplots()
            ax.plot(fluorescence_well_data["Temperature"], fluorescence_well_data["Fluorescence"])
            ax.set_title(f"Fluorescence vs Temperature (Well {well})")
            ax.set_xlabel("Temperature")
            ax.set_ylabel("Fluorescence")

        # Set a constant y-axis limit
           ax.set_ylim(0, 60000)

          ax.text(
             0.95,
             0.95,
             f"Tm D: {tm_d_value:.2f}",  # Removed ΔTm D
             transform=ax.transAxes,
             fontsize=10,
             verticalalignment="top",
             horizontalalignment="right",
             bbox=dict(facecolor="white", edgecolor="black", boxstyle="round,pad=0.3"),

                )
                st.pyplot(fig)
                if st.button(f"Save Fluorescence Plot for Well {well}", key=f"save_fluorescence_{well}"):
                    save_figure(fig, f"fluorescence_{well}.png")

                # Plot Derivative
                st.subheader(f"Derivative Plot for Well {well}")
                fig, ax = plt.subplots()
                ax.plot(derivative_well_data["Temperature"], derivative_well_data["Derivative"])
                ax.set_title(f"Derivative vs Temperature (Well {well})")
                ax.set_xlabel("Temperature")
                ax.set_ylabel("Derivative")
                ax.text(
                    0.95,
                    0.95,
                    f"Tm D: {tm_d_value:.2f}\nΔTm D: {delta_tm_d}",
                    transform=ax.transAxes,
                    fontsize=10,
                    verticalalignment="top",
                    horizontalalignment="right",
                    bbox=dict(facecolor="white", edgecolor="black", boxstyle="round,pad=0.3"),
                )
                st.pyplot(fig)
                if st.button(f"Save Derivative Plot for Well {well}", key=f"save_derivative_{well}"):
                    save_figure(fig, f"derivative_{well}.png")

        # Overlay Fluorescence Plots
        if wells_to_overlay:
            st.header("Overlay Fluorescence Plots")
            fig, ax = plt.subplots()
            for well in wells_to_overlay:
                fluorescence_well_data = fluorescence_data[fluorescence_data["Well Position"] == well]
                ax.plot(fluorescence_well_data["Temperature"], fluorescence_well_data["Fluorescence"], label=f"Well {well}")
            ax.set_title("Overlay of Fluorescence vs Temperature")
            ax.set_xlabel("Temperature")
            ax.set_ylabel("Fluorescence")
            ax.legend()
            st.pyplot(fig)
            if st.button("Save Overlay Plot"):
                save_figure(fig, "overlay_fluorescence.png")
